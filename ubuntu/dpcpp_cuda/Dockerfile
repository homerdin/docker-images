ARG ubuntu_version=22.04
FROM ghcr.io/rse-ops/ubuntu:$ubuntu_version

# Install llvm with spack
ARG cuda_version=11.4.0
ENV cuda_version=$cuda_version

RUN spack install cuda@${cuda_version}
RUN spack view --dependencies no symlink --ignore-conflicts /opt/view cuda@${cuda_version} && \
  spack compiler find
RUN git clone https://github.com/intel/llvm.git

# Install llvm:
# Needed to symlink libcuda.so stub to /opt/view/lib64 to build on
# system without Nvidia device. Needed for system with one?
RUN cd llvm && CC=gcc CXX=g++ python3 ./buildbot/configure.py --cuda --cmake-gen "Unix Makefiles" --cmake-opt="-DCUDA_TOOLKIT_ROOT_DIR=/opt/view"
RUN CC=gcc CXX=g++ python3 ./buildbot/compile.py 

ARG LLVM=/opt/llvm/build/install
ENV PATH=${LLVM}/bin:${PATH}
ENV LIBRARY_PATH=${LLVM}/lib:${LLVM}/lib/clc:${LIBRARY_PATH}
ENV LD_LIBRARY_PATH=${LLVM}/lib:${LLVM}/lib/clc:${LD_LIBRARY_PATH}
ENV CPATH=${LLVM}/include:${LLVM}/include/sycl:${CPATH}
