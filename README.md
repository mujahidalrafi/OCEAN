# OCEAN
OCEAN – <ins>O</ins>pen-source <ins>C</ins>XL <ins>E</ins>mulation at Hyperscale <ins>A</ins>rchitecture and <ins>N</ins>etworking

Compute Express Link (CXL) 3.0 introduces powerful memory pooling and promises to transform datacenter architectures. However, the lack of available CXL 3.0 hardware and the complexity of multi-host configurations pose significant challenges to the community. OCEAN is a comprehensive emulation framework that enables full CXL 3.0 functionality, including multi-host memory sharing and pooling support. OCEAN provides emulation of CXL 3.0 features—such as fabric management, dynamic memory allocation, and coherent memory sharing across multiple hosts—in advance of real hardware availability. An evaluation of OCEAN shows that it achieves performance within about 3x of projected native CXL 3.0 speeds having complete compatibility with existing CXL software stacks. We demonstrate the utility of OCEAN through a case study on Genomics Pipeline, distributed database, LLM workloads, observing up to a 15% improvement in application performance compared to traditional RDMA-based approaches.


```bash
git clone https://github.com/mujahidalrafi/OCEAN.git
cd OCEAN
sudo ip link add br0 type bridge
sudo ip link set br0 up
sudo ip addr add 192.168.100.1/24 dev br0
# Change this based on the number of hosts you want to simulate:
for i in 0 1; do
    sudo ip tuntap add tap$i mode tap
    sudo ip link set tap$i up
    sudo ip link set tap$i master br0
done
mkdir build
cd build
cmake ..
make -j$(nproc)
sudo make install
wget https://asplos.dev/about/qemu.img
wget https://asplos.dev/about/bzImage
cp qemu.img qemu1.img
../qemu_integration/launch_qemu_cxl1.sh
# in qemu
vi /usr/local/bin/*.sh
# change 192.168.100.10 to 11
vi /etc/hostname
# change node0 to node1
exit
# out of qemu
../qemu_integration/launch_qemu_cxl.sh &
../qemu_integration/launch_qemu_cxl1.sh &
```

## GROMACS
 
```bash
# Inside host 1:
export CXL_DAX_PATH="/dev/dax0.0"
export CXL_DAX_RESET=1  # Reset allocation counter on first process
export CXL_SHIM_VERBOSE=1
LD_PRELOAD=/root/libmpi_cxl_shim.so mpirun --allow-run-as-root -np 2 -hostfile hostfile -x CXL_DAX_PATH -x CXL_DAX_RESET -x CXL_SHIM_VERBOSE -x LD_PRELOAD ./gmx_mpi mdrun -s benchMEM.tpr -nsteps 10000 -resethway -ntomp 1
```
