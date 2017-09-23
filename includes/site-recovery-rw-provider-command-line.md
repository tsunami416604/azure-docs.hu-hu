UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <az adatátvitelhez használni kívánt IP-cím] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Paraméterek:

* /ServerMode: Kötelező. Megadja, hogy a konfigurációs és folyamatkiszolgálót is, vagy csak a folyamatkiszolgálót kell-e telepíteni. Bemeneti értékek: CS, PS.
* Telepítési hely: Kötelező. Az összetevők telepítési mappája.
* /MySQLCredsFilePath. Kötelező. A fájl elérési útja, amelyen az MySQL-kiszolgáló hitelesítő adatai tárolva vannak. A fájlnak a következő formátumúnak kell lennie:
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath. Kötelező. A tároló hitelesítőadat-fájljának helye
* /EnvType. Kötelező. A telepítés típusa. Értékek: VMware, NemVMware
* /PSIP és /CSIP. Kötelező. A folyamatkiszolgáló és a konfigurációs kiszolgáló IP-címe.
* /PassphraseFilePath. Kötelező. A hozzáférési fájl kódjának helye.
* /BypassProxy. Választható. Megadja, hogy a konfigurációs kiszolgáló proxy nélkül csatlakozik az Azure-hoz.
* /ProxySettingsFilePath. Választható. Proxybeállítások (Az alapértelmezett proxyhoz hitelesítés vagy egyéni proxy szükséges). A fájlnak a következő formátumúnak kell lennie:
* [ProxyBeállítások]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address>"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort. Választható. A replikált adatok portszáma.
* SkipSpaceCheck. Választható. Gyorsítótár terület-ellenőrzésének kihagyása.
* AcceptThirdpartyEULA. Kötelező. Külső felektől származó végfelhasználói licencszerződés elfogadása.
* ShowThirdpartyEULA. Kötelező. Külső felektől származó végfelhasználói licenszszerződés megjelenítése. Bemenetként való megadása esetén figyelmen kívül hagyja a többi paramétert.
