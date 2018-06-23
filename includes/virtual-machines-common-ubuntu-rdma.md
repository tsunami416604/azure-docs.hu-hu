1. Dapl, rdmacm, ibverbs és mlx4 telepítése

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. A /etc/waagent.conf engedélyezze a RDMA uncommenting a következő konfigurációs sorokat. Ez a fájl szerkesztése legfelső szintű hozzáférés szükséges.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. Adja hozzá, vagy módosítsa a következő memória beállításokat a /etc/security/limits.conf fájlban KB-ban. Ez a fájl szerkesztése legfelső szintű hozzáférés szükséges. Tesztelési célokra is beállíthatja memlock korlátlan. Például: `<User or group name>   hard    memlock   unlimited`.

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Telepítse az Intel MPI könyvtár. Vagy [beszerzési és letöltése](https://software.intel.com/intel-mpi-library/) Intel vagy letölthető a könyvtárban a [ingyenes próbaverzió](https://registrationcenter.intel.com/en/forms/?productid=1740).

  ```bash
  wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
  ```
 
 Csak Intel MPI 5.x futtatókörnyezetek támogatottak.
 
 A telepítési lépéseket lásd: a [Intel MPI könyvtár telepítési útmutató](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Engedélyezze a ptrace nem gyökércsomópont nem hibakereső folyamatok (Intel MPI legújabb verziója szükséges).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```
