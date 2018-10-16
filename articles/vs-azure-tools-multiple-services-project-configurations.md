---
title: Több szolgáltatás-konfiguráció használata az Azure projekt konfigurálása |} A Microsoft Docs
description: Ismerje meg az Azure felhőszolgáltatás-projekt konfigurálása a ServiceDefinition.csdef ServiceConfiguration.Local.cscfg és ServiceConfiguration.Cloud.cscfg fájlok módosításával.
services: visual-studio-online
author: ghogen
manager: douge
assetId: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 1ec587f4a4519f86efceb7cefa0acb372035a9a9
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318214"
---
# <a name="configuring-your-azure-project-in-visual-studio-to-use-multiple-service-configurations"></a>Az Azure-projekt konfigurálása a Visual Studióban több szolgáltatás-konfiguráció használata

Az Azure felhőszolgáltatás-projekt a Visual Studióban három konfigurációs fájlokat tartalmazza: `ServiceDefinition.csdef`, `ServiceConfiguration.Local.cscfg`, és `ServiceConfiguration.Cloud.cscfg`:

- `ServiceDefinition.csdef` van üzembe helyezve az Azure-ban a felhőszolgáltatás és a hozzá tartozó szerepkörök követelményeit ismertetik, és adja meg az összes példányra vonatkozó beállításokat. Beállítások az Azure szolgáltatás üzemeltetési futtatókörnyezeti API használata futásidőben olvasható. Ez a fájl frissíthető az Azure-ban, csak akkor, ha a felhőszolgáltatás leállítását.
- `ServiceConfiguration.Local.cscfg` és `ServiceConfiguration.Cloud.cscfg` adja meg az értékeket, a beállításokat a definícióban fájlt, és adja meg a Futtatás minden szerepkör-példányok számát. A "Local" fájlt a helyi hibakeresés; használt értékeket tartalmaz a "Felhő" fájl üzemel az Azure-ba, `ServiceConfiguration.cscfg` és a kiszolgáló környezetére vonatkozó beállításokat tartalmaz. Ez a fájl frissíthető az Azure-ban a felhőszolgáltatás futása közben.

Konfigurációs beállítások felügyelt és a Visual Studióban a alkalmazni szerepkör tulajdonságlapjain módosított (kattintson a jobb gombbal a szerepkört, és válassza ki **tulajdonságok**, vagy kattintson duplán a szerepkör). Módosítások bármelyik konfigurációs van kiválasztva a hatóköre a **szolgáltatáskonfiguráció** listából. Webes és feldolgozói szerepkörök tulajdonságait hasonlóak, kivéve, ha az alábbi szakaszok ismertetik.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

A szolgáltatásdefiníció és a szolgáltatás konfigurációs fájljait az alapul szolgáló sémák kapcsolatos információkért tekintse meg a [.csdef XML-séma](cloud-services/schema-csdef-file.md) és [.cscfg XML-séma](cloud-services/schema-cscfg-file.md) cikkeket. Szolgáltatás konfigurációjával kapcsolatos további információkért lásd: [konfigurálása a Cloud Services hogyan](cloud-services/cloud-services-how-to-configure-portal.md).


## <a name="configuration-page"></a>Konfiguráció lap

### <a name="service-configuration"></a>Szolgáltatás konfigurációja

Választja ki, amely `ServiceConfiguration.*.cscfg` fájl hatással vannak a. Alapértelmezés szerint nincsenek helyi és Felhőbeli változatok, és használhatja a **kezelése...**  másolja, átnevezése és eltávolítása a konfigurációs fájlok parancsot. Ezek a fájlok kerülnek a felhőszolgáltatási projektet, és megjelennek **Megoldáskezelőben**. Azonban átnevezése vagy konfigurációk eltávolítása csak között elvégezhető a vezérlőelem.

### <a name="instances"></a>Példányok

Állítsa be a **példány** tulajdonságot a szolgáltatást futtatni a szerepkör-példányok száma.

Állítsa be a **Virtuálisgép-méret** tulajdonságot **nagyon kicsi**, **kis**, **Közepes**, **nagy**, vagy **Extra nagy**.  További információkért lásd: [méretű felhőszolgáltatások](cloud-services/cloud-services-sizes-specs.md).

### <a name="startup-action-web-role-only"></a>Indítási műveletet (csak a webes szerepkör)

Ezzel a tulajdonsággal adja meg, hogy a Visual Studio kell elindítani a webböngészőt a HTTP-végpontokat, vagy a HTTPS-végpontok, illetve mindkettőt hibakereső indításakor.

A **HTTPS-végpont** lehetőség áll rendelkezésre, csak akkor, ha a HTTPS-végpont már megadta a szerepkörhöz. A HTTPS-végpont meghatározhatja a **végpontok** tulajdonságlap.

Ha már felvett HTTPS-végpontokat, a HTTPS-végpont beállítás alapértelmezés szerint engedélyezve van, és a Visual Studio Tallózza a végpont elindításakor mellett egy böngészőben a HTTP-végponton, hibakeresés, feltéve, hogy mindkét indítási beállítások engedélyezve vannak.

### <a name="diagnostics"></a>Diagnosztika

A webes szerepkör alapértelmezés szerint engedélyezve van a diagnosztika. Az Azure-felhő projekt- és tárolási szolgáltatásfiók vannak állítva a helyi storage emulator használata. Ha készen áll az üzembe helyezése az Azure-ba, kiválaszthatja a jelentéskészítő gombra (**...** ) inkább az Azure storage használata. A tárfiók igény szerint vagy automatikusan az ütemezett időközönként küldhetnek a diagnosztikai adatokat. Az Azure diagnostics kapcsolatos további információkért lásd: [Diagnosztikának az Azure Cloud Services és Virtual Machines](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Beállítások lap

Az a **beállítások** lapon név-érték-párokként konfigurációra beállításokat is hozzáadhat. A szerepkörben futó kódot olvashatja az osztályokkal által biztosított futásidőben a konfigurációs beállítások értékei a [Azure által felügyelt erőforrástár](http://go.microsoft.com/fwlink?LinkID=171026), pontosabban a [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) metódus.

### <a name="configuring-a-connection-string-for-a-storage-account"></a>A storage-fiókhoz tartozó kapcsolati karakterlánc konfigurálása

Kapcsolati karakterlánc a kapcsolati és hitelesítési adatait a storage emulatort, vagy egy Azure storage-fiókot biztosító beállítást. Amikor egy szerepkört a kód hozzáfér az Azure storage (blobok, üzenetsorok vagy táblák), szükséges egy kapcsolati karakterláncot.

> [!Note]
> Az Azure storage-fiókhoz tartozó kapcsolati karakterláncot egy meghatározott formátumot kell használnia (lásd: [konfigurálása az Azure Storage kapcsolati karakterláncok](storage/common/storage-configure-connection-string.md)).

Beállíthatja a kapcsolati karakterláncot a helyi tárolók használatához szükséges, majd állítsa be az Azure storage-fiókba az alkalmazás központi telepítésekor a felhőszolgáltatáshoz. Nem sikerült megfelelően beállítani a kapcsolati karakterláncot a szerepkör nem fog elindulni, vagy a inicializálása során, foglalt és leállítása állapota okozhat.

Hozzon létre egy kapcsolati karakterláncot, jelölje be **beállítás hozzáadása** és **típus** "Kapcsolati karakterlánc".

Válassza ki az új vagy meglévő kapcsolati karakterláncok **...** * jobb oldalán a **érték** mezőt, nyissa meg a **tárolási kapcsolati karakterlánc létrehozása** párbeszédpanel:

1. Alatt **használatával csatlakozzon**, válassza ki a **az előfizetés** lehetőséget válassza ki a tárfiókot az előfizetésből. A Visual Studio ezután lekéri a tárfiók hitelesítő adatai automatikusan a a `.publishsettings` fájlt.
1. Kiválasztásával **manuálisan kell megadni a hitelesítő adatok** adja meg a fiók nevét és kulcsát, közvetlenül az adatokat az Azure Portal használatával. A fiókkulcs másolásához:
    1. Lépjen a tárfiókhoz az Azure Portalon, és válassza a **kulcsok kezelése**.
    1. A fiókkulcs másolásához, lépjen a storage-fiókba az Azure Portalon, válassza a **beállítások > hozzáférési kulcsok**, a másolási gomb használatával másolja a vágólapra az elsődleges elérési kulcsot.
1. Válassza ki a kapcsolat beállítások. **Adja meg az egyedi végpontok** arra kéri, hogy a blobok, táblák, konkrét URL-címének megadása, és üzenetsorok. Egyéni végpontok lehetővé teszik, hogy [egyéni tartományok](storage/blobs/storage-custom-domain-name.md) és pontosabban, a hozzáférés szabályozásához. Lásd: [az Azure Storage kapcsolati karakterláncok konfigurálása](./storage/common/storage-configure-connection-string.md).
1. Válassza ki **OK**, majd **fájl > Mentés** a konfigurációjának frissítése az új kapcsolati karakterlánccal.

Újra ha közzéteszi az alkalmazás az Azure-ba, válassza ki a szolgáltatás konfigurációja, amely tartalmazza az Azure storage-fiókját a kapcsolati karakterláncot. Az alkalmazás közzététele után győződjön meg arról, hogy az alkalmazás megfelelően működik-e az Azure storage szolgáltatások ellen.

Szolgáltatáskonfiguráció frissítésével kapcsolatos további információkért lásd a szakasz [kezelése a storage-fiókok kapcsolati karakterláncok](vs-azure-tools-configure-roles-for-cloud-service.md#manage-connection-strings-for-storage-accounts).

## <a name="endpoints-page"></a>Végpontok lap

Webes szerepkör általában rendelkezik egy HTTP-végpontot a 80-as porton. Egy feldolgozói szerepkörben, másrészt lehet bármely HTTP, HTTPS vagy a TCP-végpontok száma. Végpontok bemeneti végpontok, amelyek külső ügyfelek számára elérhető, és a belső végpontok, amelyek elérhetők a szolgáltatásban futó egyéb szerepkörök is lehetnek.

- Ahhoz, hogy egy HTTP-végpontot érhető el a külső ügyfelek és a böngészők, módosítsa a bemeneti végpont típusát, és adjon meg egy nevet és egy nyilvános port számát.
- Ahhoz, hogy HTTPS-végpont elérhető külső ügyfelek és a böngészők, módosítsa a végpont típusát **bemeneti**, és adjon meg egy nevet, egy nyilvános port számát és egy felügyeleti tanúsítvány neve. Meg kell adnia a tanúsítvány is a **tanúsítványok** tulajdonságlap előtt megadhat egy felügyeleti tanúsítványt. 
- Ahhoz, hogy a végpont belső eléri a cloud service-ben más szerepkörök, belső végpont típusának módosítása, és adjon meg egy nevet és a lehetséges privát portok ezen a végponton.

## <a name="local-storage-page"></a>Helyi tároló lap

Használhatja a **helyi tároló** tulajdonságok lapon egy vagy több szerepkör helyi tároló-erőforrások lefoglalása. Helyi tároló egyik erőforrásához az egy fenntartott könyvtár, az Azure virtuális gépen, amelyben egy szerepkör példányának fut. a fájlrendszerben.

## <a name="certificates-page"></a>Tanúsítványok lap

A **tanúsítványok** tulajdonságlap hozzáadja a tanúsítványokkal kapcsolatos adatokat a szolgáltatás konfigurációjának. Vegye figyelembe, hogy a tanúsítványok nem vannak csomagolva az szolgáltatással; fel kell tölteni a tanúsítványok külön-külön Azure-t a [az Azure portal](http://portal.azure.com).

Itt egy tanúsítvány hozzáadása a tanúsítványokról hozzáadása a szolgáltatás konfigurációját. Tanúsítványok nem vannak csomagolva az szolgáltatással; fel kell tölteni a tanúsítványok külön-külön az Azure Portalon keresztül.

A szerepkör társítható egy tanúsítvány, adja meg a tanúsítvány nevét. Ez a név használatával tekintse meg a tanúsítványt a HTTPS-végpont konfigurálásakor a **végpontok** lapot. Ezután adja meg, hogy a tanúsítványtároló **helyi gép** vagy **aktuális felhasználó** és a tároló nevét. Végül adja meg a tanúsítvány ujjlenyomata. Ha a tanúsítványt az aktuális User\Personal (saját) tárolóban, válassza ki a tanúsítványt egy feltöltött listát is megadhatja a tanúsítvány ujjlenyomata. Ha más helyen található, adja meg manuálisan az ujjlenyomat értékét.

Amikor hozzáad egy tanúsítványt a tanúsítványtárolóból, a köztes tanúsítványok automatikusan hozzáadódnak a konfigurációs beállításokat. Emellett ezek a köztes tanúsítványok fel kell tölteni az Azure-bA megfelelően konfigurálja az SSL-hez a szolgáltatást.

Minden felügyeleti tanúsítványt, amelyet a szolgáltatás társít csak akkor, amikor a felhőben fut a szolgáltatás vonatkoznak. Amikor a szolgáltatás fut a helyi fejlesztési környezetét, az egy normál tanúsítványt a compute emulator által felügyelt.
