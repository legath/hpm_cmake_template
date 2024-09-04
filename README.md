CMAKE template for HPM SDK integration.

Use it for clion IDE and CI tools. 

Made for using with nds-gcc toolchain variant


    cmake -B build -G "Unix Makefiles" -DBUILD_CONFIG=hpm5300evk
    cmake --build build

Use menu to edit config options
    
`make -C build menuconfig`
