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

## C++

`perf record -e cycles:u -g -- npm run start`

Use https://github.com/KDAB/hotspot to visualize the results

## Javascript

1. Use the `--prof` and `--log-source` options when launching node
2. Run `<v8-dir>/tools/linux-tick-processor --preprocess v8.log > v8.json`
3. Go to https://v8.github.io/tools/head/profview/index.html and load the resulting json file
