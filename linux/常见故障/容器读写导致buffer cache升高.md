# 原因
* 在linux中每一次的读写 都会触发 buffer/cache ，这是为了保护磁盘而内置的一套机制.
* 而buffer/cache会使用物理内存，因此为了不使容器内的读写导致宿主机的buffer/cache升高 .
# 解决
* 需要给容器加上内存限制，因为linux释放buffer/cache的机制是内存不足的时候才会触发