1. Dapl, rdmacm, ibverbs és mlx4 telepítése

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. Az /etc/waagent.conf engedélyezéséhez rdma-t a következő konfigurációs sorok uncommenting. Ez a fájl szerkesztése a legfelső szintű hozzáférésre van szüksége.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. Adja hozzá, vagy módosítsa a /etc/security/limits.conf fájlban KB memória a következő beállításokat. Ez a fájl szerkesztése a legfelső szintű hozzáférésre van szüksége. Tesztelési célokra és korlátlan memlock megadható. Például: `<User or group name>   hard    memlock   unlimited`.

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Telepítse az Intel MPI-könyvtár. Bármelyik [vásárol, és töltse le](https://software.intel.com/intel-mpi-library/) Intel és a letöltés a kódtárat a [ingyenes próbaverzió](https://registrationcenter.intel.com/en/forms/?productid=1740).

  ```bash
  wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
  ```
 
 Csak az Intel MPI 5.x modulok támogatottak.
 
 A telepítés lépéseit, tekintse meg a [Intel MPI könyvtár telepítési útmutató](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Engedélyezze a ptrace nem gyökér nem hibakereső folyamatok (Intel MPI legújabb verziója szükséges).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```
