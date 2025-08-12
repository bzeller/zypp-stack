Zypp-Stack 
==========

The zypp-stack repository provides a way to compile and work on the full libsolv/libzypp/zypper stack via one CMake project.
Libzypp will be build with special switches that make it search for supporting binaries in the build directories instead
of the system install paths first, so a installed libzypp can not interfere with the binaries in the build dir. However, this
also means the binaries built with zypp-stack are not out of the box suitable to be used with "make install".

Getting started
---------------
Clone the repository from github and apply the libsolv patch:
```   
git clone --recursive https://github.com/openSUSE/zypp-stack.git

# currently we need to patch libsolv manually
pushd zypp-stack/libsolv
git apply ../libsolv.patch

# back to root directory
popd
```

That's it, now we are ready to build:
```
cd zypp-stack
mkdir build
cd build
cmake ..
make
```

Todo
----
Moving forward the zypp-stack repository might provide a Dockerfile to create a builder container with all required depencencies.
