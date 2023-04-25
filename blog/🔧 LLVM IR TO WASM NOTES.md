---
date created: 2023-04-25
date modified: 2023-04-25
tags: [linux,os,tools]
title: 🔧 LLVM IR TO WASM NOTES
---
## Description
Just a note for C++ -> LLVM IR -> wasm
## Environments
❄️ nix-shell :
```shell
$ nix-shell -p llvm clang llvmPackages.llvm  llvmPackages.lld llvmPackages.libcxx llvmPackages.libcxxabi wasmer wasmtime
```
## Code
### IR generator
```C++
#include <iostream>
#include <llvm/ADT/APFloat.h>
#include <llvm/IR/BasicBlock.h>
#include <llvm/IR/Constants.h>
#include <llvm/IR/DerivedTypes.h>
#include <llvm/IR/Function.h>
#include <llvm/IR/IRBuilder.h>
#include <llvm/IR/LLVMContext.h>
#include <llvm/IR/Module.h>
#include <llvm/IR/Type.h>
#include <llvm/IR/Verifier.h>

using namespace llvm;

int main() {
    LLVMContext context;
    IRBuilder<> builder(context);
    std::unique_ptr<Module> module = std::make_unique<Module>("helloworld", context);

    // Declare "puts" function
    std::vector<Type *> puts_args;
    puts_args.push_back(Type::getInt8PtrTy(context));
    FunctionType *puts_func_type = FunctionType::get(Type::getInt32Ty(context), puts_args, false);
    Function *puts_func = Function::Create(puts_func_type, Function::ExternalLinkage, "puts", module.get());

    // Create main function
    FunctionType *main_func_type = FunctionType::get(Type::getInt32Ty(context), false);
    Function *main_func = Function::Create(main_func_type, Function::ExternalLinkage, "main", module.get());

    // Create basic block for main function
    BasicBlock *entry_block = BasicBlock::Create(context, "entry", main_func);
    builder.SetInsertPoint(entry_block);

    // Create "Hello, World!" constant string
    Constant *hello_world = builder.CreateGlobalStringPtr("Hello, World!\n");

    // Call "puts" function with "Hello, World!" as argument
    builder.CreateCall(puts_func, hello_world);

    // Return 0
    builder.CreateRet(ConstantInt::get(Type::getInt32Ty(context), 0));

    // Verify main function
    verifyFunction(*main_func);

    // Print generated IR
    module->print(outs(), nullptr);

    return 0;
}
```

###  Build & Generate IR
```shell
$ clang++ -std=c++11 `llvm-config --cxxflags --ldflags --libs core` -o ll.out ll.cpp
$ ./ll.out

; ModuleID = 'helloworld'
source_filename = "helloworld"

@0 = private unnamed_addr constant [15 x i8] c"Hello, World!\0A\00", align 1

declare i32 @puts(i8*)

define i32 @main() {
entry:
  %0 = call i32 @puts(i8* getelementptr inbounds ([15 x i8], [15 x i8]* @0, i32 0, i32 0))
  ret i32 0
}

$ ./ll.out >> ll.ll
$ llc -march=wasm32 -filetype=obj hello.ll -o hello.o
$ wasm-ld --no-entry --export-dynamic --allow-undefined --import-memory hello.o -o hello.wasm
```

### Run hello.wasm by nodejs runtim

```js
const fs = require('fs');
const wasmCode = fs.readFileSync('hello.wasm');

const wasmImports = {
  env: {
    memory: new WebAssembly.Memory({ initial: 256 }),
    memoryBase: 0,
    table: new WebAssembly.Table({ initial: 0, element: 'anyfunc' }),
    tableBase: 0,
    puts: (ptr) => {
      const memory = new Uint8Array(wasmImports.env.memory.buffer);
      let str = '';
      while (memory[ptr] !== 0) {
        str += String.fromCharCode(memory[ptr]);
        ptr++;
      }
      console.log(str);
    },
  },
};

WebAssembly.instantiate(wasmCode, wasmImports)
  .then((wasmModule) => {
    const mainFunc = wasmModule.instance.exports.main;
    mainFunc();
  })
  .catch((error) => {
    console.error('Error:', error);
  });
```
