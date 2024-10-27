## 编译libffmpeg.so
```bash
cd ffmpeg-6.1.2
./configure --prefix=/usr/local/ffmpeg-6.1.2 --enable-shared --disable-static
make -n > build.txt
make -j 32
cp fftools/ffmpeg.c fftools/ffmpeg2.c

sed -i 's/^int main/int command/1' ffmpeg2.c

printf "CC\t%s\n" fftools/ffmpeg.o; 
gcc -I. -I./ -D_ISOC99_SOURCE -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE -D_POSIX_C_SOURCE=200112 -D_XOPEN_SOURCE=600 -DPIC -DZLIB_CONST \
    -std=c11 -fomit-frame-pointer -fPIC -pthread -DWITH_GZFILEOP -g -Wdeclaration-after-statement \
    -Wall -Wdisabled-optimization -Wpointer-arith -Wredundant-decls -Wwrite-strings -Wtype-limits -Wundef -Wmissing-prototypes -Wstrict-prototypes \
    -Wempty-body -Wno-parentheses -Wno-switch -Wno-format-zero-length -Wno-pointer-sign -Wno-unused-const-variable -Wno-bool-operation \
    -Wno-char-subscripts -O3 -fno-math-errno -fno-signed-zeros -fno-tree-vectorize -Werror=format-security -Werror=implicit-function-declaration \
    -Werror=missing-prototypes -Werror=return-type -Werror=vla -Wformat -fdiagnostics-color=auto -Wno-maybe-uninitialized \
    -MMD -MF fftools/ffmpeg.d -MT fftools/ffmpeg.o -c -o fftools/ffmpeg2.o fftools/ffmpeg2.c

printf "LD\t%s\n" libffmpeg.so;
gcc -shared -o libffmpeg.so \
    -Llibavcodec -Llibavdevice -Llibavfilter -Llibavformat -Llibavutil -Llibpostproc -Llibswscale -Llibswresample \
    fftools/ffmpeg_dec.o fftools/ffmpeg_demux.o fftools/ffmpeg_enc.o fftools/ffmpeg_filter.o fftools/ffmpeg_hw.o \
    fftools/ffmpeg_mux.o fftools/ffmpeg_mux_init.o fftools/ffmpeg_opt.o fftools/objpool.o fftools/sync_queue.o fftools/thread_queue.o \
    fftools/cmdutils.o fftools/opt_common.o fftools/ffmpeg2.o \
    -lavdevice -lavfilter -lavformat -lavcodec -lswresample -lswscale -lavutil \
    -lm -latomic -lxcb -lxcb-shm -lxcb-shape -lxcb-xfixes -pthread -lbz2 -lz -llzma -lX11 \
    -Wl,--as-needed -Wl,-z,noexecstack -Wl,--warn-common

cp libffmpeg.so ${your_project}/lib/
```

## 复制FFmpeg源代码
```bash
project_path=`pwd`
cd ffmpeg-6.1.2/fftools
cp ffmpeg_dec.c ffmpeg_demux.c ffmpeg_enc.c ffmpeg_filter.c ffmpeg_hw.c \
    ffmpeg_mux.c ffmpeg_mux_init.c ffmpeg_opt.c objpool.c sync_queue.c thread_queue.c \
    cmdutils.c opt_common.c ffmpeg.c $project_path/src/libffmpeg 
```
  