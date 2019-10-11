---
title: SQL Server Integration Services (SSIS) csomagok végrehajtása az Azure-t támogató dtexec segédprogrammal | Microsoft Docs
description: Ismerje meg, hogyan hajthat végre SQL Server Integration Services (SSIS) csomagokat az Azure-t támogató dtexec segédprogrammal.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 740e53728356755bcc42e1e0aafb64992b30e113
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249031"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>SQL Server Integration Services (SSIS) csomagok futtatása az Azure-t támogató dtexec segédprogrammal
Ez a cikk az Azure-kompatibilis **dtexec** (**AzureDTExec**) parancssori segédprogramot ismerteti.  Azure-SSIS Integration Runtime (IR) Azure Data Factory (ADF) SSIS-csomagjainak futtatására használatos.

A hagyományos **dtexec** segédprogram SQL Server tartalmaz, további információért lásd a [dtexec segédprogram](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) dokumentációját.  A SSIS-csomagok helyszíni futtatásához gyakran harmadik féltől származó kezelők/ütemező (például aktív batch, Control-M stb.) is hivatkozik.  A modern **AzureDTExec** segédprogram SQL Server Management Studio (SSMS) eszközt tartalmaz.  A SSIS-csomagok Azure-ban való futtatásához harmadik féltől származó szervezők vagy ütemező is hivatkozhat.  Megkönnyíti a SSIS-csomagok felhőbe való áthelyezését és áttelepítését &.  Ha a Migrálás után szeretné tovább használni a harmadik féltől származó szervezőket/ütemező szolgáltatásokat a napi műveletekben, akkor a **dtexec**helyett **AzureDTExec** hivatkozhatnak.

A **AzureDTExec** az ADF-folyamatokban hajtja végre a csomagokat, mint a SSIS-csomag tevékenységeit, további információért lásd: [SSIS-csomagok futtatása ADF-tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) cikk.  A SSMS-on keresztül konfigurálható olyan Azure Active Directory (HRE) alkalmazás használatára, amely folyamatokat hoz létre az ADF-ben.  Az is beállítható úgy, hogy hozzáférhessen a fájlrendszerek/fájlmegosztás/Azure Files eléréséhez, ahol a csomagokat tárolja.  A Meghívási beállításokhoz megadott értékek alapján a **AzureDTExec** létrehoz és futtat egy egyedi ADF-folyamatot, és végrehajtja a SSIS-csomag végrehajtásával kapcsolatos tevékenységet.  A **AzureDTExec** ugyanazzal az értékekkel való meghívása a meglévő folyamat újrafuttatására is lehetőséget ad.

## <a name="prerequisites"></a>Előfeltételek
A **AzureDTExec**használatához töltse le és telepítse a legújabb SSMS (18,3-es vagy újabb verziót) [innen.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)

## <a name="configure-azuredtexec-utility"></a>AzureDTExec segédprogram konfigurálása
A SSMS telepítése a helyi gépen a **AzureDTExec**-t is telepíti.  A beállítások konfigurálásához indítsa el a SSMS-t a **Futtatás rendszergazdaként** beállítással, és válassza a lépcsőzetes legördülő menü elemének **eszközei – > Migrálás az Azure-ba – > Az Azure-kompatibilis DTExec konfigurálása**.

![Az Azure-kompatibilis dtexec menü konfigurálása](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Ez a művelet megnyit egy olyan **AzureDTExecConfig** ablakot, amelyet rendszergazdai jogosultságokkal kell megnyitni a **AzureDTExec. Settings** fájlba való íráshoz.  Ha nem rendszergazdaként futtatja a SSMS-t, a felhasználói fiókok felügyelete (UAC) ablakban megadhatja a rendszergazdai jelszót a jogosultságok emelése érdekében.

![Az Azure-t támogató dtexec beállításainak konfigurálása](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

A **AzureDTExecConfig** ablakban a következőképpen adhatja meg a konfigurációs beállításokat:

- **ApplicationId**: adja meg annak a HRE-alkalmazásnak az egyedi azonosítóját, amelyet a megfelelő engedélyekkel hoz létre a folyamatok az ADF-ben való létrehozásához. További információért tekintse meg a [HRE-alkalmazás és egyszerű szolgáltatás létrehozása Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) cikkben.

- **AuthenticationKey**: adja meg a HRE-alkalmazás hitelesítési kulcsát.

- **TenantId**: adja meg a HRE-bérlő egyedi azonosítóját, amely alatt LÉTREJÖN a HRE-alkalmazás.

- **SubscriptionId**: adja meg az Azure-előfizetés egyedi azonosítóját, amely alatt az ADF-et létrehozták.

- **ResourceGroup**: adja meg az Azure-erőforráscsoport nevét, amelyben az ADF-et létrehozták.

- **DataFactory**: adja meg az ADF nevét, amelyben az SSIS-csomag végrehajtásával kapcsolatos egyedi folyamatok a **AzureDTExec**meghívásakor megadott beállítások alapján jönnek létre.

- **IRName**: adja meg Azure-SSIS IR nevét az ADF-ben, amelyen az univerzális elnevezési KONVENCIÓ (UNC) elérési útján megadott csomagok futnak a **AzureDTExec** meghívásakor.

- **PackageAccessDomain**: adja meg a tartományi hitelesítő adatokat a csomagok eléréséhez a **AzureDTExec**meghívásakor megadott UNC-útvonalon.

- **PackageAccessUserName**: adja meg a username hitelesítő adatait, amely a **AzureDTExec**meghívásakor megadott UNC elérési úton fér hozzá a csomagjaihoz.

- **PackageAccessPassword**: adja meg a jelszó hitelesítő adatait a csomagok eléréséhez a **AzureDTExec**meghívásakor megadott UNC-útvonalon.

- **LogPath**: adja meg a log mappa UNC elérési útját, amelybe a rendszer a csomagok végrehajtásáról Azure-SSIS IR naplófájlokat ír.

- **Naplózási szint**: adja meg a naplózás kiválasztott hatókörét az előre definiált **Null**@no__t – 2**alapszintű**@no__t – 4**részletes**/**teljesítményi** lehetőségekkel a csomagok végrehajtásához Azure-SSIS IRon.

- **LogAccessDomain**: adja meg azt a tartományi hitelesítő adatot, amely a naplófájlok írásakor való eléréséhez szükséges a log mappához az UNC-elérési úton, ha **LogPath** van megadva, és a **naplózási szint** nem **null értékű**.

- **LogAccessUserName**: adja meg azt a felhasználónevet, amely a log mappához való hozzáféréshez szükséges a naplófájlok írásakor, ha a **LogPath** meg van adva, és a **naplózási szint** nem **null értékű**.

- **LogAccessPassword**: adja meg azt a jelszót, amely a log mappához való hozzáféréshez szükséges a naplófájlok írásakor, ha a **LogPath** meg van adva, és a **naplózási szint** nem **null értékű**.

- **PipelineNameHashStrLen**: adja meg a **AzureDTExec**meghívásakor megadott beállításokból generált kivonatoló karakterláncok hosszát.  A karakterláncok a csomagokat a Azure-SSIS IRon futtató ADF-folyamatok egyedi neveinek megalkotása céljából lesznek felhasználva.  Általában 32 karakter hosszúnak kell lennie.

Ha azt tervezi, hogy a csomagokat és a naplófájlokat a helyszínen tárolja a fájlrendszerek/fájlmegosztás területén, csatlakoztassa a Azure-SSIS IRt a helyszíni hálózathoz csatlakoztatott VNet, hogy beolvassa a csomagokat, és megírja a naplófájlokat, lásd: [csatlakozás Azure-SSIS IR VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) További információ a cikkből.

Ha el szeretné kerülni, hogy a **AzureDTExec. Settings** fájlba írt bizalmas értékek egyszerű szövegként legyenek megjelenítve, a rendszer a Base64 kódolású karakterláncba kódolja őket.  A **AzureDTExec**meghívásakor az összes Base64 kódolású karakterlánc vissza lesz kódolva az eredeti értékükre.  A **AzureDTExec. Settings** fájlt továbbra is biztonságossá teheti az ahhoz hozzáférő fiókok korlátozásával.

## <a name="invoke-azuredtexec-utility"></a>AzureDTExec segédprogram meghívása
A **AzureDTExec** meghívhatja a parancssorba, és megadhatja a használati esetnek megfelelő értékeket a megadott beállításokhoz.

A segédprogram a `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` számítógépen van telepítve. Az elérési utat hozzáadhatja az "ELÉRÉSi út" környezeti változóhoz, amelyet a rendszer bárhonnan meghívhat.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

A **AzureDTExec** meghívása hasonló lehetőségeket kínál a **dtexec**meghívásához, további információért lásd a [dtexec segédprogram](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) dokumentációját.  A jelenleg támogatott lehetőségek a következők:

- **/F [Ile]** : a fájlrendszer/fájlmegosztás/Azure Files tárolt csomag betöltése.  A beállítás értékeként megadhatja a csomagfájl UNC elérési útját a fájlrendszer/fájlmegosztás/Azure Files a saját dtsx-bővítménnyel.  Ha a megadott UNC elérési út bármilyen helyet tartalmaz, idézőjeleket kell elhelyeznie a teljes elérési útra.

- **/Conf [igFile]** : meghatározza az értékek kinyerésére szolgáló konfigurációs fájlt.  Ezzel a beállítással megadhatja a csomag futásidejű konfigurációját, amely eltér a tervezési időpontban megadott értéktől.  Egy XML-konfigurációs fájlban különböző beállításokat tárolhat, majd betöltheti őket a csomag végrehajtása előtt.  További információért lásd a [SSIS-csomag konfigurációját](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017) ismertető cikket.  A beállítás értékeként megadhatja a konfigurációs fájl UNC elérési útját a fájlrendszer/fájlmegosztás/Azure Files a saját dtsConfig bővítménnyel.  Ha a megadott UNC elérési út bármilyen helyet tartalmaz, idézőjeleket kell elhelyeznie a teljes elérési útra.

- **/Conn [csolat]** : a csomag meglévő kapcsolataihoz tartozó kapcsolatok karakterláncait határozza meg.  Ezzel a beállítással beállíthatja, hogy a csomagban lévő, a tervezési időpontban megadott beállításoktól eltérő futásidejű kapcsolatok karakterláncai meglegyenek a csomag meglévő kapcsolataihoz.  A beállítás értékeként a következőt adhatja meg: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.

- **Készlet**: felülbírálja a paraméter, változó, tulajdonság, tároló, naplózási szolgáltató, foreach enumerálás vagy a csomagbeli kapcsolatok konfigurációját.  Ez a beállítás többször is megadható.  A beállítás értékeként a következőt adhatja meg: `property_path;value`, például `\package.variables[counter].Value;1` felülbírálja a `counter` változó értékét 1-ként.  A csomag konfigurációja varázslóval megkeresheti, másolhatja és beillesztheti `property_path` értékét a csomagban található azon elemekhez, amelynek értékét felül szeretné bírálni: további információért tekintse meg a [csomag konfigurálása varázsló](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014) dokumentációját.

- **/De [Crypt]** : a **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** védelmi szinttel konfigurált csomag visszafejtési jelszavának beállítása.

> [!NOTE]
> A **AzureDTExec** új értékekkel való meghívása új folyamatot fog eredményezni, kivéve a **/de [csfájl]** kapcsolót.

## <a name="next-steps"></a>Következő lépések

Ha a SSIS-csomag végrehajtásával kapcsolatos egyedi folyamatok jönnek létre és futnak a **AzureDTExec**meghívása után, akkor az ADF-portálon figyelhetők. További információért lásd: [SSIS-csomagok futtatása ADF-tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) .

> [!WARNING]
> A létrehozott folyamatot a rendszer csak a **AzureDTExec**fogja használni. A tulajdonságok/paraméterek a jövőben változhatnak, ezért ne módosítsa/ne használja fel azokat más célra, ami megszakíthatja a **AzureDTExec**. Ha ez történik, bármikor törölheti a folyamatot, és a **AzureDTExec** a következő meghívásakor új folyamatot fog előállítani.
