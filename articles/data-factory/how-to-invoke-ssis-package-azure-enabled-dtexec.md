---
title: SQL Server Integration Services (SSIS) csomagok végrehajtása az Azure-kompatibilis dtexec segédprogrammal
description: Ismerje meg, hogyan hajtható végre az SQL Server Integration Services (SSIS) csomagok az Azure-kompatibilis dtexec segédprogrammal.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: 006d4fa9ed09170a423e796e893b817e079e861b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261931"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>SQL Server Integration Services-csomagok futtatása az Azure-kompatibilis dtexec segédprogrammal
Ez a cikk az Azure-kompatibilis dtexec (AzureDTExec) parancssori segédprogramot ismerteti. Az SQL Server Integration Services (SSIS) csomagok futtatására szolgál az Azure-Data Factory Ban az Azure-SSIS-integrációs futásidőben (IR).

A hagyományos dtexec segédprogram az SQL Server szolgáltatással rendelkezik. További információ: [dtexec utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Gyakran hivatkoznak külső orchestrators vagy ütemezők, például az ActiveBatch és a Control-M, ssis-csomagok helyszíni futtatásához. 

A modern AzureDTExec segédprogram egy SQL Server Management Studio (SSMS) eszközzel érkezik. Azt is meglehet hívni a külső orchestrators vagy ütemezők SSIS-csomagok futtatásához az Azure-ban. Megkönnyíti az SSIS-csomagok felhőbe történő áthelyezését, áthelyezését vagy migrálását. Az áttelepítés után, ha továbbra is szeretné használni a külső vezénylők vagy ütemezők a napi műveletek, most már meghívhat AzureDTExec helyett dtexec.

Az AzureDTExec a csomagokat SSIS-csomagtevékenységek végrehajtásaként futtatja a Data Factory-folyamatokban. További információ: [SSIS-csomagok futtatása Azure Data Factory-tevékenységekként.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) 

Az AzureDTExec konfigurálható az SSMS-en keresztül egy Azure Active Directory (Azure AD) alkalmazás használatára, amely folyamatokat hoz létre az adat-előállítóban. Azt is be lehet állítani, hogy file systems, fájlmegosztások, vagy az Azure Files, ahol a csomagok tárolására. A meghívási beállításokhoz megadott értékek alapján az AzureDTExec létrehoz és futtat egy egyedi Data Factory-folyamatot, amelyben SSIS-csomag végrehajtása tevékenység et hajt végre. Az AzureDTExec meghívása a beállítások hoz a beállításokhoz ugyanazokat az értékeket újrafuttatja a meglévő folyamat.

## <a name="prerequisites"></a>Előfeltételek
Az AzureDTExec használatához töltse le és telepítse az SSMS legújabb verzióját, amely a 18.3-as vagy újabb verzió. Töltse le [erről a honlapról](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-the-azuredtexec-utility"></a>Az AzureDTExec segédprogram konfigurálása
Az SSMS telepítése a helyi számítógépre is telepíti az AzureDTExec-et. A beállítások konfigurálásához indítsa el az SSMS-t a **Futtatás rendszergazdaként** beállítással. Ezután válassza **az Eszközök** > **áttelepítése az Azure-ba** > **az Azure-kompatibilis DTExec**lehetőséget.

![Az Azure-kompatibilis dtexec menü konfigurálása](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Ez a művelet megnyit egy **AzureDTExecConfig** ablakot, amelyet rendszergazdai jogosultságokkal kell megnyitni ahhoz, hogy az *AzureDTExec.settings* fájlba való íráshoz. Ha rendszergazdaként nem futtatta az SSMS-t, megnyílik egy Felhasználói fiókok felügyelete (UAC) ablak. Adja meg rendszergazdai jelszavát a jogosultságok emelése érdekében.

![Azure-kompatibilis dtexec-beállítások konfigurálása](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Az **AzureDTExecConfig** ablakban adja meg a konfigurációs beállításokat az alábbiak szerint:

- **ApplicationId:** Adja meg az Azure AD alkalmazás egyedi azonosítóját, amelyet az adat-előállító ban folyamatok létrehozásához megfelelő engedélyekkel hoz létre. További információ: [Create an Azure AD app and service principal via Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
- **AuthenticationKey**: Adja meg az Azure AD-alkalmazás hitelesítési kulcsát.
- **TenantId:** Adja meg az Azure AD-bérlő egyedi azonosítóját, amely alatt az Azure AD-alkalmazás jön létre.
- **DataFactory:** Adja meg az adatgyár nevét, amelyben egyedi folyamatok végrehajtása SSIS csomag tevékenység azok ban jönlétre az AzureDTExec meghívásakor megadott beállítások értékei alapján.
- **IRName**: Adja meg az Azure-SSIS ir nevét az adat-előszobában, amelyen az Univerzális elnevezési konvenció (UNC) elérési útján megadott csomagok az AzureDTExec meghívásakor futnak.
- **PipelineNameHashStrLen**: Adja meg az AzureDTExec meghívásakor megadott beállítások értékeiből létrehozandó kivonatos karakterláncok hosszát. A karakterláncok a Data Factory-folyamatok egyedi neveinek létrehozásához használatosak, amelyek a csomagokat az Azure-SSIS IR-en futtatják. Általában 32 karakter hosszúsága elegendő.
- **ResourceGroup**: Adja meg annak az Azure-erőforráscsoportnak a nevét, amelyben az adatgyár at létrehozták.
- **SubscriptionId**: Adja meg az Azure-előfizetés egyedi azonosítóját, amely alatt az adatgyár at létrehozták.
- **LogAccessDomain**: Adja meg a tartomány hitelesítő adatait a naplómappa UNC elérési útján való eléréséhez a naplófájlok írásakor, amelyre akkor van szükség, ha a **LogPath** meg van adva, és a **LogLevel** nem **null**.
- **LogAccessPassword**: Adja meg a jelszó hitelesítő adatát a naplómappa UNC elérési útján való eléréséhez a naplófájlok írásakor, amelyre akkor van szükség, ha a **LogPath** meg van adva, és a **LogLevel** nem **null**.
- **LogAccessUserName**: Adja meg a felhasználónév hitelesítő adatát, hogy a naplómappát unc elérési útján férje ni naplófájlok írásakor, ami szükséges, ha **a LogPath** meg van adva, és **a LogLevel** nem **null**.
- **LogLevel**: Adja meg a kiválasztott naplózási hatókört előre definiált **null,** **Alapszintű**, **részletes**vagy **teljesítmény** beállításokat a csomag végrehajtások az Azure-SSIS IR.
- **LogPath**: Adja meg a naplómappa UNC elérési útját, amelybe az Azure-SSIS ir-en lévő csomagvégrehajtás-végrehajtások naplófájljai nak írása történik.
- **PackageAccessDomain**: Adja meg a tartományi hitelesítő adatokat a csomagok eléréséhez az AzureDTExec meghívásakor megadott UNC elérési útjukon.
- **PackageAccessPassword:** Adja meg a jelszó hitelesítő adatokat a csomagok eléréséhez az UNC elérési útján, amely az AzureDTExec meghívásakor megadott.
- **PackageAccessUserName**: Adja meg a felhasználónév hitelesítő adatait a csomagok eléréséhez az AzureDTExec meghívásakor megadott UNC elérési útjukon.

A csomagok és a naplófájlok fájlrendszerekben vagy a helyszíni fájlmegosztásokban való tárolásához csatlakozzon az Azure-SSIS ir-hez egy, a helyszíni hálózathoz csatlakoztatott virtuális hálózathoz, hogy letudja olvasni a csomagokat, és megírhassa a naplófájlokat. További információ: [Csatlakozás az Azure-SSIS infravörös hálózathoz virtuális hálózathoz című témakörben.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

Annak elkerülése érdekében, hogy az *AzureDTExec.settings* fájlba egyszerű szövegként írt bizalmas értékeket jelenítsük meg, base64 kódolási karakterláncokba kódoljuk őket. Az AzureDTExec meghívásakor az összes Base64 kódolású karakterlánc visszalesz kódolva az eredeti értékeikbe. Az *AzureDTExec.settings* fájl további biztonságossá tehető az ahhoz hozzáféréssel rendelkező fiókok korlátozásával.

## <a name="invoke-the-azuredtexec-utility"></a>Az AzureDTExec segédprogram meghívása
Meghívhatja az AzureDTExec-et a parancssori parancssorból, és a használati forgatókönyvben adott beállításokhoz a megfelelő értékeket adhat meg.

A segédprogram a `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`rendszerhez van telepítve. Az elérési útját hozzáadhatja a "PATH" környezeti változóhoz, hogy bárhonnan meghívható legyen.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Az AzureDTExec meghívása hasonló lehetőségeket kínál, mint a dtexec meghívása. További információ: [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017). Az alábbiakban a jelenleg támogatott lehetőségek et tetszetős:

- **/F[ile]**: Betölt egy csomagot, amely a fájlrendszerben, a fájlmegosztásban vagy az Azure Files ban van tárolva. Ennek a beállításnak az értékeként megadhatja a csomagfájl UNC elérési útját a fájlrendszerben, a fájlmegosztásban vagy az Azure Files -ban a .dtsx kiterjesztésével. Ha a megadott UNC elérési út szóközt tartalmaz, tegyen idézőjeleket a teljes útvonal köré.
- **/Conf[igFile]**: Megadja azt a konfigurációs fájlt, amelyből ki szeretné nyerni az értékeket. Ezzel a beállítással beállíthatja a csomag futásidejű konfigurációját, amely eltér a tervezési időben megadotttól. A különböző beállításokat xml-konfigurációs fájlban tárolhatja, majd a csomag végrehajtása előtt betöltheti. További információ: [SSIS-csomagkonfigurációk](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017). A beállítás értékének megadásához használja a konfigurációs fájl UNC elérési útját a fájlrendszerben, a fájlmegosztásban vagy az Azure Files dtsConfig kiterjesztésével. Ha a megadott UNC elérési út szóközt tartalmaz, tegyen idézőjeleket a teljes útvonal köré.
- **/Conn[ection]**: A csomagban lévő meglévő kapcsolatkezelők kapcsolati karakterláncait adja meg. Ezzel a beállítással beállíthatja a csomagban lévő meglévő kapcsolatkezelők futásidejű kapcsolati karakterláncait, amelyek eltérnek a tervezés időpontjában megadottaktól. Adja meg ennek a beállításnak `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`az értékét a következőképpen: .
- **/Set**: Felülbírálja egy paraméter, változó, tulajdonság, tároló, naplószolgáltató, Foreach enumerátor vagy kapcsolat konfigurációját a csomagban. Ez a beállítás többször is megadható. Adja meg ennek a beállításnak `property_path;value`az értékét a következőképpen: . Például `\package.variables[counter].Value;1` felülírja a `counter` változó értékét 1-ként. A **Csomagkonfigurálás** varázslóval megkeresheti, másolhatja `property_path` és beillesztheti a csomagazon elemeinek értékét, amelyek értékét felül szeretné bírálni. További információt a [Csomagkonfigurálás varázsló ban talál.](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)
- **/De[crypt]**: Beállítja a csomag visszafejtési jelszavát, amely az **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** védelmi szinttel van konfigurálva.

> [!NOTE]
> Az AzureDTExec új értékekkel való meghívása új folyamatot hoz létre, kivéve a **/De[cript]** kapcsolót.

## <a name="next-steps"></a>További lépések

Miután az AzureDTExec meghívásakor egyedi folyamatok jönnek létre és futnak a Végrehajtás SSIS-csomag tevékenységgel, azok figyelhetők a Data Factory portálon. A Data Factory eseményindítókat is hozzárendelheti, ha a Data Factory használatával szeretné vezénylni/ütemezni őket. További információ: [SSIS-csomagok futtatása adatgyári tevékenységként.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

> [!WARNING]
> A létrehozott folyamat várhatóan csak az AzureDTExec által használt. Tulajdonságai vagy paraméterei a jövőben változhatnak, ezért ne módosítsa vagy használja fel őket más célokra. A módosítások megszakíthatják az AzureDTExec-et. Ha ez történik, törölje a folyamatot. Az AzureDTExec a következő meghíváskor új folyamatot hoz létre.