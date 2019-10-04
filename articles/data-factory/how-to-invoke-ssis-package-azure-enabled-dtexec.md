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
ms.openlocfilehash: 80d4bd2f4f9ea4c451f312f05fd42fbc1ba433ab
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181326"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>SQL Server Integration Services (SSIS) csomagok futtatása az Azure-t támogató dtexec segédprogrammal
Ez a cikk az Azure-kompatibilis **dtexec** (**AzureDTExec**) parancssori segédprogramot ismerteti.  Azure-SSIS Integration Runtime (IR) Azure Data Factory (ADF) SSIS-csomagjainak futtatására használatos.

A hagyományos **dtexec** segédprogram SQL Server tartalmaz, további információért lásd a [dtexec segédprogram](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) dokumentációját.  A SSIS-csomagok helyszíni futtatásához gyakran harmadik féltől származó kezelők/ütemező (például aktív batch, Control-M stb.) is hivatkozik.  A modern **AzureDTExec** segédprogram SQL Server Management Studio (SSMS) eszközt tartalmaz.  A SSIS-csomagok Azure-ban való futtatásához harmadik féltől származó szervezők vagy ütemező is hivatkozhat.  Megkönnyíti a SSIS-csomagok felhőbe való áthelyezését és áttelepítését &.  Ha a Migrálás után szeretné tovább használni a harmadik féltől származó szervezőket/ütemező szolgáltatásokat a napi műveletekben, akkor a **dtexec**helyett **AzureDTExec** hivatkozhatnak.

A **AzureDTExec** az ADF-folyamatokban hajtja végre a csomagokat, mint a SSIS-csomag tevékenységeit, további információért lásd: [SSIS-csomagok futtatása ADF-tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) cikk.  A SSMS-on keresztül konfigurálható olyan Azure Active Directory (HRE) alkalmazás használatára, amely folyamatokat hoz létre az ADF-ben.  Az is beállítható úgy, hogy hozzáférhessen a fájlrendszerek/fájlmegosztás/Azure Files eléréséhez, ahol a csomagokat tárolja.  A Meghívási beállításokhoz megadott értékek alapján a **AzureDTExec** létrehoz és futtat egy egyedi ADF-folyamatot, és végrehajtja a SSIS-csomag végrehajtásával kapcsolatos tevékenységet.  A **AzureDTExec** ugyanazzal az értékekkel való meghívása a meglévő folyamat újrafuttatására is lehetőséget ad.  A **AzureDTExec** új értékekkel való meghívása új folyamatot hoz majd elérhetővé.

## <a name="prerequisites"></a>Előfeltételek
A **AzureDTExec**használatához töltse le és telepítse a legújabb SSMS (18,3-es vagy újabb verziót) [innen.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)

## <a name="configure-azuredtexec-utility"></a>AzureDTExec segédprogram konfigurálása
A SSMS telepítése a helyi gépen a **AzureDTExec**-t is telepíti.  A beállítások konfigurálásához indítsa el a SSMS-t a **Futtatás rendszergazdaként** beállítással, és válassza a lépcsőzetes legördülő menü elemének **eszközei – > Migrálás az Azure-ba – > Az Azure-kompatibilis DTExec konfigurálása**.

![Az Azure-kompatibilis dtexec menü konfigurálása](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Ez a művelet megnyit egy olyan **AzureDTExecConfig** ablakot, amelyet rendszergazdai jogosultságokkal kell megnyitni a **AzureDTExec. Settings** fájlba való íráshoz.  Ha nem rendszergazdaként futtatja a SSMS-t, a felhasználói fiókok felügyelete (UAC) ablakban megadhatja a rendszergazdai jelszót a jogosultságok emelése érdekében.

![Az Azure-t támogató dtexec beállításainak konfigurálása](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

A **AzureDTExecConfig** ablakban a következőképpen adhatja meg a konfigurációs beállításokat:

- **ApplicationId**: Adja meg annak a HRE-alkalmazásnak az egyedi azonosítóját, amelyet a megfelelő engedélyekkel hoz létre a folyamatok automatikus lapadagolóba való létrehozásához. További információért lásd: [create a HRE app and Service Principal on Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) article.

- **AuthenticationKey**: Adja meg a HRE-alkalmazás hitelesítési kulcsát.

- **TenantId**: Adja meg a HRE-bérlő egyedi azonosítóját, amely alatt a HRE-alkalmazás létrejött.

- **SubscriptionId**: Adja meg az Azure-előfizetés egyedi azonosítóját, amely alatt az ADF-et létrehozták.

- **ResourceGroup**: Adja meg az Azure-erőforráscsoport nevét, amelyben az ADF-et létrehozták.

- **DataFactory**:  Adja meg az ADF nevét, amelyben az SSIS-csomag végrehajtásával kapcsolatos egyedi folyamatok jönnek létre a **AzureDTExec**meghívásakor megadott beállítások alapján.

- **IRName**: Adja meg az automatikus lapadagolóba Azure-SSIS IR nevét, amelyen a **AzureDTExec** meghívásakor az univerzális elnevezési KONVENCIÓ (UNC) elérési útján megadott csomagok futni fognak.

- **PackageAccessDomain**: Adja meg a tartomány hitelesítő adatait a csomagok eléréséhez a **AzureDTExec**meghívásakor megadott UNC-útvonalon.

- **PackageAccessUserName**:  Adja meg a username hitelesítő adatait a csomagok eléréséhez a **AzureDTExec**meghívásakor megadott UNC-útvonalon.

- **PackageAccessPassword**: Adja meg a jelszó hitelesítő adatait a csomagok eléréséhez a **AzureDTExec**meghívásakor megadott UNC-útvonalon.

- **LogPath**:  Adja meg a log mappa UNC elérési útját, amelybe a rendszer a csomagok végrehajtásához tartozó naplófájlokat Azure-SSIS IR fogja írni.

- **Naplózási szint**:  Adja meg a naplózás kiválasztott hatókörét az előre definiált **Null**/**alapszintű**/**részletes**/**teljesítmény** -beállítások közül a csomagok végrehajtásához Azure-SSIS IRon.

- **LogAccessDomain**: Adja meg azt a tartományi hitelesítő adatot, amely a naplófájlok írásakor való eléréséhez szükséges a log mappához az UNC-elérési úton, ha **LogPath** van megadva, és a **naplózási szint** nem **null értékű**.

- **LogAccessUserName**: A naplófájlok írásakor a log mappához való hozzáféréshez adja meg a Felhasználónév hitelesítő adatait, ha a **LogPath** meg van adva, és a **naplózási szint** nem **null értékű**.

- **LogAccessPassword**: A naplófájlok írásakor a log mappához való hozzáféréshez adja meg a jelszó hitelesítő adatait, ha a **LogPath** meg van adva, és a **naplózási szint** nem **null értékű**.

- **PipelineNameHashStrLen**: Adja meg a **AzureDTExec**meghívásakor megadott beállításokból generált kivonatoló karakterláncok hosszát.  A karakterláncok a csomagokat a Azure-SSIS IRon futtató ADF-folyamatok egyedi neveinek megalkotása céljából lesznek felhasználva.  A 32 karakter hosszúsága elegendő.

Ha azt tervezi, hogy a csomagokat és a naplófájlokat a helyszínen tárolja a fájlrendszerek/fájlmegosztás területén, csatlakoztassa a Azure-SSIS IRt a helyszíni hálózathoz csatlakoztatott VNet, hogy beolvassa a csomagokat, és megírja a naplófájlokat, lásd: [csatlakozás Azure-SSIS IR VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) További információ a cikkből.

Ha el szeretné kerülni, hogy a **AzureDTExec. Settings** fájlba írt bizalmas értékek egyszerű szövegként legyenek megjelenítve, a rendszer a Base64 kódolású karakterláncba kódolja őket.  A **AzureDTExec**meghívásakor az összes Base64 kódolású karakterlánc vissza lesz kódolva az eredeti értékükre.  A **AzureDTExec. Settings** fájlt továbbra is biztonságossá teheti az ahhoz hozzáférő fiókok korlátozásával.

## <a name="invoke-azuredtexec-utility"></a>AzureDTExec segédprogram meghívása
Meghívhatja a **AzureDTExec** a parancssorba, és megadhatja a megfelelő értékeket a használati eseti forgatókönyvben, például:

`AzureDTExec.exe
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"
  /Set \package.variables[MyVariable].Value;MyValue
  /De MyEncryptionPassword`

A **AzureDTExec** meghívása hasonló lehetőségeket kínál a **dtexec**meghívásához, további információért lásd a [dtexec segédprogram](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) dokumentációját.  A jelenleg támogatott lehetőségek a következők:

- **/F [Ile]** : A fájlrendszer/fájlmegosztás/Azure Files tárolt csomag betöltése.  A beállítás értékeként megadhatja a csomagfájl UNC elérési útját a fájlrendszer/fájlmegosztás/Azure Files a saját dtsx-bővítménnyel.  Ha a megadott UNC elérési út bármilyen helyet tartalmaz, idézőjeleket kell elhelyeznie a teljes elérési útra.

- **/Conf [igFile]** : Meghatározza az értékek kinyerésére szolgáló konfigurációs fájlt.  Ezzel a beállítással megadhatja a csomag futásidejű konfigurációját, amely eltér a tervezési időpontban megadott értéktől.  Egy XML-konfigurációs fájlban különböző beállításokat tárolhat, majd betöltheti őket a csomag végrehajtása előtt.  További információért lásd a [SSIS-csomag konfigurációját](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017) ismertető cikket.  A beállítás értékeként megadhatja a konfigurációs fájl UNC elérési útját a fájlrendszer/fájlmegosztás/Azure Files a saját dtsConfig bővítménnyel.  Ha a megadott UNC elérési út bármilyen helyet tartalmaz, idézőjeleket kell elhelyeznie a teljes elérési útra.

- **/Conn [csolat]** : Megadja a csomag meglévő kapcsolataihoz tartozó kapcsolatok karakterláncait.  Ezzel a beállítással beállíthatja, hogy a csomagban lévő, a tervezési időpontban megadott beállításoktól eltérő futásidejű kapcsolatok karakterláncai meglegyenek a csomag meglévő kapcsolataihoz.  A beállítás értékeként megadhatja a következőt: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.

- **Készlet**: Felülbírálja a paraméter, a változó, a tulajdonság, a tároló, a naplózási szolgáltató, a foreach-számbavétel vagy a csomagbeli kapcsolatok konfigurációját.  Ez a beállítás többször is megadható.  A beállítás értékeként a következőt adhatja meg: `property_path;value`, például `\package.variables[counter].Value;1` `counter` felülbírálja a változó értékét 1-ként.  A csomag konfigurációja varázslóval megkeresheti, másolhatja és beillesztheti a csomagban `property_path` lévő azon elemek értékét, amelyek értékeit felül szeretné bírálni. További információért lásd a [csomag konfigurálása varázsló](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014) dokumentációját.

- **/De [Crypt]** : A **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** védelmi szintjével konfigurált csomag visszafejtési jelszavának beállítása.

## <a name="next-steps"></a>További lépések
Ha a SSIS-csomag végrehajtásával kapcsolatos egyedi folyamatokat a rendszer a **AzureDTExec**meghívása után hozza létre és futtatja, akkor az ADF-portálon a [SSIS-csomagok futtatása ADF-tevékenységként](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) című cikkben talál további információt.
