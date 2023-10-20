You need a clang that can target WASI (version 15 in the command line, you might need to adapt if you are using other version), and the WASIX Sysroot

You also need to have build zlib, lzma and openssl for wasix.

You need to adapt the command line with your own path:

```bash
./configure --progs-suffix=".wasm" --enable-cross-compile --sysroot=/PATH/TO/sysroot32 --nm=llvm-nm-15 --ar=llvm-ar-15 --cc="clang-15 --target=wasm32-wasi" --cxx="clang-15 --target=wasm32-wasi" --ld="clang-15 --target=wasm32-wasi" --strip=llvm-strip-15 --target-os=none --disable-ffplay --prefix=/PATH/TO/ffmpeg.wasm --extra-cflags="-matomics -mbulk-memory -mmutable-globals -pthread -mthread-model posix -ftls-model=local-exec -fno-trapping-math -D_WASI_EMULATED_MMAN -D_WASI_EMULATED_SIGNAL -D_WASI_EMULATED_PROCESS_CLOCKS -O2 -g -I/PATH/TO/zlib -I/PATH/TO/openssl/include -I/PATH/TO/liblzma/src/liblzma/api" --extra-ldflags="-Wl,--shared-memory -Wl,--max-memory=4294967296 -Wl,--import-memory -Wl,--export-dynamic -Wl,--export=__heap_base -Wl,--export=__stack_pointer -Wl,--export=__data_end -Wl,--export=__wasm_init_tls -Wl,--export=__wasm_signal -Wl,--export=__tls_size -Wl,--export=__tls_align -Wl,--export=__tls_base -lwasi-emulated-mman -lwasi-emulated-process-clocks -O2 -g" --extra-libs="-L/PATH/TO/zlib -L/PATH/TO/openssl/ -L/PATH/TO/liblzma/src/liblzma/.libs/" --disable-asm --enable-openssl --disable-debug --enable-static --disable-shared --ranlib=llvm-ranlib-15
```

This should configure with lots of module (too many to list them there).

Build with `make` and install to your target folder `/PATH/TO/ffmpeg.wasm` with `make install`


You might need to asyncify `ffmpeg.wasm` and `ffprobe.wasm` with 

```bash
wasm-opt --asyncify -O -ocimfs=1 /PATH/TO/ffmpeg_wasm/bin/ffmpeg.wasm -o /PATH/TO/ffmpeg_wasm/bin/ffmpeg.wasm
wasm-opt --asyncify -O -ocimfs=1 /PATH/TO/ffmpeg_wasm/bin/ffprobe.wasm -o /PATH/TO/ffmpeg_wasm/bin/ffprobe.wasm
```
