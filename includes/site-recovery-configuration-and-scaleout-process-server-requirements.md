| **Összetevő** | **Követelmény** |
| --- |---|
| Processzormagok| 8 |
| RAM | 12 GB|
| Lemezek száma | 3., beleértve az operációs rendszer lemez, a folyamat kiszolgáló gyorsítótár lemez és a feladat-visszavételi adatmegőrzési meghajtó |
| – Lemez szabad területe (Folyamatkiszolgálói gyorsítótár) | 600 GB
| Lemez szabad területe (adatmegőrzési lemez) | 600 GB|
| Operációs rendszer  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Operációs rendszer területi beállítása | Angol (en-us)|
| VMware vSphere PowerCLI verziója | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server-szerepkörök | Ezek a szerepkörök nem engedélyezi: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V |
| Csoportházirendek| A csoportházirendek nem engedélyezi: <br> -Tagadni a hozzáférést a parancssorba. <br> -A hozzáférés megakadályozása a beállításjegyzék szerkesztésével eszközök. <br> – Megbízható vonatkozó logikát. <br> -Parancsfájl végrehajtása bekapcsolása. <br> [További információ](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx)|
| IIS | -Nincs elérésű, korábban létező alapértelmezett webhely <br> -Engedélyezése [névtelen hitelesítés](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx) beállítás  <br> -Egyetlen elérésű, korábban létező webhely vagy alkalmazás figyeli a 443-as port<br>|
| A hálózati adapter típusa | VMXNET3 (ha VMware virtuális gépként telepített) |
| IP-cím típusa | Statikus |
| Internetelérés | A kiszolgáló URL-hozzáférésre van szüksége: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> -https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (kibővített folyamat kiszolgálók nem kötelező) <br> - time.nist.gov <br> - time.windows.com |
| Portok | 443 (vezérlőcsatorna-vezénylés)<br>9443 (Adatátvitel)|
