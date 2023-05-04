# Node Native Addons

To generate a compile_commands.json file for a node gyp project, run this command
```
node-gyp -- configure -f=gyp.generator.compile_commands_json.py
```
To include Nan.h headers in Clangd, create a file named .clangd in the root with this text:
```
CompileFlags:
  Add:
    - "--include-directory=~/.nvm/versions/node/v12.22.7/lib/node_modules/nan/"
```

# Performance monitoring

The best source if you have questions or are looking for guidance is [Brendan Gregg's website](https://www.brendangregg.com/perf.html).

## Perf Permissions

Perf has access to a lot of low level data, and at it's highest mode of running, it has access to CPU registers for any process on the system. Given it's access to sensitive data, security for perf is pretty locked down. Many commmands require access to the `/sys/kernel/tracing` directory.

I reccomend

  1. Creating a new user group called `tracing`
  2. Adding your user to that group
  3. Making that group owner of the `/sys/kernel/tracing` directory

The alternative is giving your user CAP_PERFMON capabilities.

If you still get permission denied errors when running perf commands, try running `sudo mount -o remount,mode=755 /sys/kernel/tracing/`

## C++

### Running Perf

`perf record -e cycles:u -g -- npm run start`

Until the fix for [this issue](https://lore.kernel.org/lkml/20230427012841.231729-1-yangjihong1@huawei.com/T/#u) is in upstream (which may be a while), you'll need to manually install perf, otherwise you'll get a buffer overflow anytime the pylon library is loaded.


### Visualizing

Use https://github.com/KDAB/hotspot to visualize the results

### Listing Symbols and Dynamic Tracing

Dynamic tracing is useful if you want to dynamically probe events, such as when the program enters and exits a function. This can be useful if you're only interested in how long a small subset of function take or if you're interested in the *"true"* time that a function takes to complete, as opposed to just the on-CPU time.

`perf probe -F --no-demangle --filter '*' -m --file-name-here.o--`

In order to list the symbols, you need to probe the .o file at the bottom of the cpp directory. However, to add the probe, you should target the \*.node file under *gpp-events/build/Debug/*, or wherever the compiled node file is.

This website explains the reasoning for many of the options: http://notes.secretsauce.net/notes/2019/12/16_c-probes-with-perf.html

So the add command will look something like this

`sudo perf probe --no-demangle -x ./gpp_events.node --add _ZN18HistogramDatastore9AddEventsEP17HistogramRawFrame`

The last step is telling perf that you want to recrod data for the probes you just ran. You can use `perf probe -l` to list available probes, but it will typically be something like `probe_gpp_events`. Your command for running perf will be

`perf record -e probe_gpp_events:* -g -- npm run start`

## Javascript

1. Use the `--prof` and `--log-source` options when launching node
2. Run `<v8-dir>/tools/linux-tick-processor --preprocess v8.log > v8.json`
3. Go to https://v8.github.io/tools/head/profview/index.html and load the resulting json file
