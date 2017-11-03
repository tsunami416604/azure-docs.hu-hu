| **Hardver** | |
| --- |---|
| Processzormagok száma| 8 |
| RAM| 12 GB|
| Lemezek száma | 3 <br><br> – Operációsrendszer-lemez<br> – Folyamatkiszolgálói gyorsítótárlemez<br> – Adatmegőrzési meghajtó (feladat-visszavételhez)|
| – Lemez szabad területe (Folyamatkiszolgálói gyorsítótár) | 600 GB
| Lemez szabad területe (adatmegőrzési lemez) | 600 GB|
| **Szoftver** | |
| Operációs rendszer verziója | Windows Server 2012 R2 <br> Windows Server 2016 |
| Operációs rendszer területi beállítása | Angol (en-us)|
| VMware vSphere PowerCLI verziója | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server-szerepkörök | Ne engedélyezze a következő szerepköröket: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V |
| Csoportházirendek| A következő csoportházirendek nem engedélyezni kell a kiszolgálón <br> -Hozzáférés megakadályozása a parancssorból <br> -Hozzáférés megakadályozása a beállításjegyzék szerkesztésével eszközök <br> -Megbízhatósági fájlmellékletek logika <br> -Kapcsolja be a parancsfájl végrehajtása <br> **Megjegyzés:** található további információ a csoportházirendek [Itt](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| Internet információk Service(IIS) beállításai | -Nincs már meglévő alapértelmezett webhely <br> -Engedélyezése [névtelen hitelesítés](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx) beállítás  <br> -Egyetlen meglévő websit alkalmazás figyelésére kell a 443-as port<br>|
| **Hálózat** | |
| Hálózati adapter típusa | VMXNET3 |
| IP-cím típusa | Statikus |
| Internetelérés | A kiszolgálónak el kell tudnia érni az alábbi URL-címeket közvetlenül vagy egy proxykiszolgálón keresztül: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (nem szükséges kibővíthető folyamatkiszolgálókhoz) <br> - time.nist.gov <br> - time.windows.com |
| Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel)|
