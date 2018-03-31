---
title: A több szolgáltatáskonfiguráció használata Azure-projekt konfigurálása |} Microsoft Docs
description: Útmutató az Azure-felhőszolgáltatás-projekt konfigurálása a ServiceDefinition.csdef ServiceConfiguration.Local.cscfg és ServiceConfiguration.Cloud.cscfg fájlok módosításával.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 919568922ada2d842233ade029a54d474b4a1a0e
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="configuring-your-azure-project-in-visual-studio-to-use-multiple-service-configurations"></a>A Visual Studio több szolgáltatáskonfiguráció használata az Azure-projekt konfigurálása

A Visual Studio egy Azure-felhőszolgáltatás-projekt tartalmazza mindhárom konfigurációs fájlt: `ServiceDefinition.csdef`, `ServiceConfiguration.Local.cscfg`, és `ServiceConfiguration.Cloud.cscfg`:

- `ServiceDefinition.csdef` a rendszer az Azure-ba, és a felhőszolgáltatás és a szerepkörök követelményeinek leírására, és beállítások megadását összes példányára vonatkoznak. Az Azure szolgáltatást üzemeltető futásidejű API-val futásidőben beállítások olvasható. Ez a fájl frissíthető Azure csak akkor, ha a felhőalapú szolgáltatás le van állítva.
- `ServiceConfiguration.Local.cscfg` és `ServiceConfiguration.Cloud.cscfg` adjon meg értékeket, a beállítások a definícióban fájlt, és adja meg az egyes szerepkörökhöz futtatásához példányainak száma. A "Helyi" fájlt a helyi hibakeresés; használt értékeket tartalmaz a "Cloud" fájlt a rendszer Azure-bA `ServiceConfiguration.cscfg` és a kiszolgáló környezetre vonatkozó beállításokat tartalmaz. Ez a fájl frissíthető az Azure-ban a felhőalapú szolgáltatás futása közben.

Konfigurációs beállítások felügyelete, a Visual Studio használatával tulajdonságlapokat a megfelelő szerepkör módosításukra (kattintson a jobb gombbal a szerepkört, és válassza ki **tulajdonságok**, vagy kattintson duplán a szerepkör). Módosítások hatóköre beállítható úgy, hogy bármelyik konfigurációs van kiválasztva a a **szolgáltatáskonfiguráció** legördülő listán. A webes és feldolgozói szerepkörök tulajdonságainak hasonló, kivéve, ha az alábbi szakaszok ismertetik.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

A szolgáltatásdefiníció és a szolgáltatás konfigurációs fájljait az alapul szolgáló sémák kapcsolatos információkért tekintse meg a [.csdef XML-séma](cloud-services/schema-csdef-file.md) és [.cscfg XML-séma](cloud-services/schema-cscfg-file.md) cikkeket. Szolgáltatás konfigurációjával kapcsolatos további információkért lásd: [felhőalapú szolgáltatások konfigurálása](cloud-services/cloud-services-how-to-configure-portal.md).


## <a name="configuration-page"></a>Konfiguráció lap

### <a name="service-configuration"></a>Service Configuration

Kiválasztása `ServiceConfiguration.*.cscfg` változások által érintett fájl. Alapértelmezés szerint nincsenek helyi és Felhőbeli használata, és használhatja a **kezelése...**  parancs, nevezze át, és távolítsa el a konfigurációs fájlok. Ezeket a fájlokat a felhőszolgáltatás-projekt adnak, és megjelennek **Megoldáskezelőben**. Azonban átnevezése vagy konfigurációk eltávolítása végezhető csak a vezérlőről.

### <a name="instances"></a>Példányok

Állítsa be a **példány** száma a szolgáltatás fusson a szerepkör-példányok száma tulajdonságot.

Állítsa be a **Virtuálisgép-méretet** tulajdonságot **Extra Small**, **kis**, **Közepes**, **nagy**, vagy **Extra nagy**.  További információkért lásd: [Felhőszolgáltatások mérete](cloud-services/cloud-services-sizes-specs.md).

### <a name="startup-action-web-role-only"></a>Indítási műveletet (csak a webes szerepkör)

Ez a tulajdonság adja meg, hogy a Visual Studio minden kell elindítani a webböngészőt a HTTP-végpontokról vagy a HTTPS-végpontnak, vagy mindkét, hibakereső indításakor beállítása.

A **a HTTPS-végpont** lehetőség áll rendelkezésre, csak akkor, ha már definiált HTTPS-végpontnak a szerepkörhöz. A HTTPS-végpontnak meghatározhatja a **végpontok** tulajdonságlapján.

Ha már hozzáadta a HTTPS-végpontnak, a HTTPS-végpont beállítás alapértelmezés szerint engedélyezve van, és a Visual Studio elindítja a böngésző ezen a végponton indításakor hibakeresési információ mellett a böngészőt a HTTP-végpontot, feltéve, hogy mindkét indítási beállítások engedélyezve vannak.

### <a name="diagnostics"></a>Diagnosztika

A webes szerepkör alapértelmezés szerint engedélyezve vannak a diagnosztika. Az Azure-felhőbe projekt- és tárolási szolgáltatásfiókot a helyi storage emulator használata van beállítva. Ha készen áll az Azure telepítéséhez, kiválaszthatja a jelentéskészítő gombra (**...** ) használhatja az Azure storage is. A diagnosztikai adatok vihetők át a tárolási fiók, az igény szerinti vagy automatikusan rendszeres időközönként. Az Azure diagnostics kapcsolatos további információkért lásd: [diagnosztika engedélyezésével az Azure Cloud Services és a virtuális gépek](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Beállítások lap

Az a **beállítások** lapon adhat hozzá beállítások olyan konfigurációt név-érték párként. A szerepkör futó elolvashatják által biztosított osztályokat futásidőben a konfigurációs beállítások értékének a [Azure felügyelt kódtár](http://go.microsoft.com/fwlink?LinkID=171026), pontosabban a [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) metódus.

### <a name="configuring-a-connection-string-for-a-storage-account"></a>A tárfiók kapcsolati karakterláncot konfigurálása

A kapcsolati karakterlánca a beállítást, amely kapcsolati és hitelesítési információkat nyújt a storage emulator vagy egy Azure storage-fiókot. Szerepkör kód az Azure storage (BLOB, üzenetsorok vagy táblák) fér hozzá, amikor jogcímadatokat egy kapcsolati karakterláncot.

> [!Note]
> Azure-tárfiók kapcsolati karakterláncot a definiált formátumot kell használnia (lásd: [konfigurálása Azure Storage kapcsolati karakterláncok](storage/common/storage-configure-connection-string.md)).

A kapcsolati karakterláncot helyi tároló használatára, szükség esetén, majd állítsa be az Azure-tárfiók az alkalmazás központi telepítésekor a felhőalapú szolgáltatás állíthatja be. Nem sikerült megfelelően beállítani a kapcsolati karakterlánc okozhat a szerepkör nem fog elindulni, vagy a inicializálása, foglalt és leállítása állapota.

A kapcsolati karakterlánc létrehozásához válassza **beállítás hozzáadása** és **típus** "Kapcsolati karakterlánc".

Válassza ki az új vagy meglévő kapcsolati karakterláncok **...** * jobb oldalán a **érték** mező megnyitása a **tárolási kapcsolati karakterlánc létrehozása** párbeszédpanel:

1. A **protokoll használatával kapcsolódó levelezőprogramokkal**, válassza ki a **az előfizetés** kiválaszthatja azokat a tárfiók előfizetésből. A Visual Studio majd beolvassa a tárfiók hitelesítő adatainak automatikusan a a `.publishsettings` fájlt.
1. Kiválasztása **manuálisan kell megadni a hitelesítő adatok** segítségével adja meg a fiók nevét, valamint kulcs közvetlenül az Azure-portálról információk alapján. A fiók kulcsának az átmásolása: egy. Keresse meg az Azure portál, és válassza ki a tárfiók **kulcsok kezelése**.
    2. A fiók kulcsának az átmásolása, keresse meg az Azure portálon, válassza ki a tárfiók **beállítások > hívóbetűk**, majd használja az elsődleges elérési kulcsot a vágólapra másolni a Másolás gombra.
1. A kapcsolódási beállítások valamelyikét kell választani. **Egyéni végpontokat határoz meg** arra kéri, hogy a blobok, táblák, adott URL-címének megadása, és várólistára helyezi. Egyéni végpontokkal használatát [egyéni tartományok](storage/blobs/storage-custom-domain-name.md) és pontosabban, a hozzáférés vezérlése érdekében. Lásd: [konfigurálása az Azure Storage kapcsolati karakterláncok](./storage/common/storage-configure-connection-string.md).
1. Válassza ki **OK**, majd **fájl > Mentés** frissíteni a beállításait az új kapcsolati karakterlánccal.

Újra ha közzéteszi a az alkalmazás az Azure-ba, válassza a szolgáltatás konfigurációja, amely tartalmazza az Azure storage-fiók a kapcsolódási karakterláncban. Az alkalmazás közzététele után győződjön meg arról, hogy az alkalmazás megfelelően működik-e az Azure storage szolgáltatásainak ellen.

Frissítse a szolgáltatáskonfiguráció kapcsolatos további információkért lásd: a szakasz [kapcsolati karakterláncok storage-fiókok kezelése](vs-azure-tools-configure-roles-for-cloud-service.md#manage-connection-strings-for-storage-accounts).

## <a name="endpoints-page"></a>Végpontok lap

A webes szerepkör egyetlen HTTP-végpont jellemzően rendelkezik 80-as porton. A feldolgozói szerepkör, másrészt több HTTP, HTTPS vagy TCP-végpontok is rendelkezhetnek. Végpontok lehetnek a bemeneti végpontok, amelyek külső ügyfelek számára elérhető, vagy a belső végpontok, a szolgáltatásban futó egyéb szerepkörök számára elérhető.

- Egy HTTP-végpont elérhető külső ügyfelek és a böngészők, módosítsa a végpont típusát a felhasználótól, és adjon meg egy nevet és egy nyilvános port számát.
- Ha HTTPS-végpontnak szeretné elérhetővé tenni külső ügyfelek és a böngészők, a végpont típusának módosítása **bemeneti**, és adjon meg egy nevet, egy nyilvános port számát és a felügyeleti tanúsítvány nevét. Akkor is definiálnia kell a tanúsítványt a a **tanúsítványok** tulajdonságlapján előtt egy felügyeleti tanúsítványt. 
- A végpont belső hozzáférés elérhetővé teszi a felhőszolgáltatásban található más szerepkörök, belső végpont típusának módosítása, és adja meg egy nevet és a lehetséges titkos portok ezen a végponton.

## <a name="local-storage-page"></a>Helyi tároló lap

Használhatja a **helyi tároló** tulajdonságlapján egy vagy több szerepkör helyi tároló-erőforrásokat lefoglalni. A helyi tároló egyik erőforrásához egy fenntartott könyvtárat az Azure virtuális gép szerepkör példánya fut. a fájlrendszerben.

## <a name="certificates-page"></a>Tanúsítványok lap

A **tanúsítványok** tulajdonságlapján hozzáadja a tanúsítványokkal kapcsolatos adatokat a szolgáltatás konfigurációját. Vegye figyelembe, hogy a tanúsítványok nem vannak csomagolva az szolgáltatással; fel kell tölteni a tanúsítványok külön Azure keresztül a [Azure-portálon](http://portal.azure.com).

A tanúsítványokkal kapcsolatos információkat itt szerepel tanúsítvány hozzáadása hozzáadása a szolgáltatás konfigurációját. Tanúsítványok nem vannak csomagolva az szolgáltatással; a tanúsítványok, az Azure portálon keresztül külön-külön kell feltöltenie.

Egy tanúsítványt a szerepkörrel társítani, adja meg a tanúsítvány nevét. Ez a név segítségével tekintse meg a tanúsítványt a HTTPS-végpontnak konfigurálásakor a **végpontok** lap. Ezt követően adja meg, hogy a tanúsítványtároló **helyi számítógép** vagy **aktuális felhasználó** és a tároló neve. Végül adja meg a tanúsítvány ujjlenyomatát. Ha a tanúsítvány a jelenlegi User\Personal (a) tároló, a a tanúsítvány ujjlenyomata a tanúsítvány kijelölésével adatokat tartalmazó listát is megadhatja. Bármely más helyen található, adja meg kézzel az ujjlenyomat értékét.

Amikor egy tanúsítványt a tanúsítványtárolóból, összes köztes tanúsítvány automatikusan a konfigurációs beállításokat. Emellett a köztes tanúsítványok fel kell tölteni az Azure-ba, a szolgáltatás az SSL megfelelő konfigurálásához.

Bármely felügyeleti tanúsítványok társítani a szolgáltatás csak akkor, amikor a felhőben fut a szolgáltatás vonatkozik. Ha a szolgáltatás fut a helyi fejlesztési környezetben, azt a compute emulator által kezelt szabványos tanúsítványt használ.
