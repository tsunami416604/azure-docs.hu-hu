|Paraméter neve| Típus | Leírás| Lehetséges értékek|
|-|-|-|-|
| /ServerMode|Kötelező|Megadja, hogy a konfigurációs és folyamatkiszolgálót is, vagy csak a folyamatkiszolgálót kell-e telepíteni.|CS<br>PS|
|/InstallLocation|Kötelező|Az összetevők telepítési mappája| A számítógép bármely mappája|
|/MySQLCredsFilePath|Kötelező|A fájl elérési útja, amelyen a MySQL-kiszolgáló hitelesítő adatai tárolva vannak|A fájlnak az alább megadott formátumúnak kell lennie|
|/VaultCredsFilePath|Kötelező|A tároló hitelesítőadat-fájljának elérési útja|Érvényes fájlelérési út|
|/EnvType|Kötelező|A védeni kívánt környezet típusa |VMware<br>NonVMware|
|/PSIP|Kötelező|A replikációs adatátvitelhez használni kívánt hálózati adapter IP-címe| Bármilyen érvényes IP-cím|
|/CSIP|Kötelező|Annak a hálózati adapternek az IP-címe, amelyen a konfigurációs kiszolgáló figyel| Bármilyen érvényes IP-cím|
|/PassphraseFilePath|Kötelező|A jelszófájl teljes elérési útja|Érvényes fájlelérési út|
|/BypassProxy|Optional|Megadja, hogy a konfigurációs kiszolgáló proxy nélkül csatlakozik az Azure-hoz.|Az érték beszerzése innen: Venu|
|/ProxySettingsFilePath|Optional|Proxybeállítások (Az alapértelmezett proxyhoz hitelesítés vagy egyéni proxy szükséges).|A fájlnak az alább megadott formátumúnak kell lennie|
|DataTransferSecurePort|Optional|Az adatreplikációhoz használni kívánt PSIP-port száma| Érvényes portszám (az alapértelmezett érték 9433)|
|/SkipSpaceCheck|Optional|Gyorsítótárlemez terület-ellenőrzésének kihagyása| |
|/AcceptThirdpartyEULA|Kötelező|Ez a jelölő a külső féltől származó végfelhasználói licencszerződés elfogadását jelzi| |
|/ShowThirdpartyEULA|Optional|Külső felektől származó végfelhasználói licenszszerződés megjelenítése. Bemenetként való megadása esetén figyelmen kívül hagyja a többi paramétert.| |


<!--HONumber=Feb17_HO4-->


