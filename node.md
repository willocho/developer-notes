#Node Native Addons

To generate a compile_commands.json file for a node gyp project, run this command
```
node-gyp -- configure -f=gyp.generator.compile_commands_json.py
```
To include Nan.h headers in Clangd, create a file named .clangd in the root with this text:
```
CompileFlags:
  Add:
    - "--include-directory=/home/manager/.nvm/versions/node/v12.22.7/lib/node_modules/nan/"
```
