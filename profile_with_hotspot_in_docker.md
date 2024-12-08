### How to profile C++ code with Hotspot in Docker

1. On host computer:
```bash
echo 0 | sudo tee /proc/sys/kernel/kptr_restrict
echo 0 | sudo tee /proc/sys/kernel/perf_event_paranoid
```
for gui support also needed:
```bash
xhost +
```
2. Launch docker image with flag `--cap-add PERFMON` (or `--privilleged`) and with gui support:
example:  
```bash
docker run --rm -it \
    --mount type=bind,source=$PWD,target=/workspace \
    -e DISPLAY \
    -e NVIDIA_VISIBLE_DEVICES=all \
    -e NVIDIA_DRIVER_CAPABILITIES=all \
    -v /tmp/.X11-unix:/tmp/.X11-unix:rw \
    -v $HOME/.Xauthority:/root/.Xauthority:rw \
    --cap-add PERFMON \
    ubuntu:focal \
    bash
```
3. Install perf and [hotspot](https://github.com/KDAB/hotspot) in container:
```bash
apt update
apt install linux-tools-generic
apt install hotspot
```
if you use Ubuntu version < 20.04, then compile hotspot from source.  

4. Compile your programm with debug symbols:
For example in CMakeLists.txt add flag:  
```cmake
set(CMAKE_BUILD_TYPE RelWithDebInfo)
```

5. Find path to perf:
```bash
find /usr/lib -type f -executable -name perf
```
For Ubuntu 20.04 it will give: /usr/lib/linux-tools-5.4.0-200/perf

6. Use found path to perf with hotspot
```bash
export PATH=/usr/lib/linux-tools-5.4.0-200/:$PATH
hotspot
```

Also you can record perf log without hotspot:
```bash
export/usr/lib/linux-tools-5.4.0-200/perf record --call-graph dwarf --aio --sample-cpu --pid 1530143
```

