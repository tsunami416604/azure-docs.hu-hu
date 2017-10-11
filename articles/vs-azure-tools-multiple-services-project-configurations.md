---
title: "A több szolgáltatáskonfiguráció használata Azure-projekt konfigurálása |} Microsoft Docs"
description: "Útmutató az Azure-felhőszolgáltatás-projekt konfigurálása a ServiceDefinition.csdef és ServiceConfiguration.cscfg fájlok módosításával."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: a4fb79ed-384f-4183-9f74-5cac257206b9
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: deb69101e855bcad56b9212736c52ace72631f0a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>A több szolgáltatáskonfiguráció használata Azure-projekt konfigurálása
Egy Azure-felhőszolgáltatás-projekt tartalmazza a két konfigurációs fájlok: ServiceDefinition.csdef és ServiceConfiguration.cscfg. Ezeket a fájlokat az Azure cloud service alkalmazással csomagolva, és az Azure-bA telepítve.

* A **ServiceDefinition.csdef** fájl tartalmazza a metaadatokat, amelyek szükségesek az Azure a felhő szolgáltatásalkalmazás, milyen szerepkörök tartalmaz, beleértve a követelményeket. Ez a fájl is tartalmazza az összes példányára vonatkozó konfigurációs beállításokat. Ezeket a konfigurációs beállításokat az Azure szolgáltatást üzemeltető futásidejű API-val futásidőben olvasható. Ez a fájl nem frissíthető, miközben fut a szolgáltatás az Azure-ban.
* A **ServiceConfiguration.cscfg** fájl értékek beállítása a a szolgáltatásdefiníciós fájlban meghatározott konfigurációs beállítás, és az egyes szerepkörökhöz futtatásához példányok számát adja meg. Ez a fájl frissíthető az Azure-ban a felhőalapú szolgáltatás futása közben.

A Microsoft Visual Studio Azure eszközei tulajdonságlapjain, melyekkel a fájlokban tárolt konfigurációs beállítások megadásához adjon meg. A tulajdonságlapok, kattintson duplán a szerepkör hivatkozás alatt az Azure cloud service projektben a Megoldáskezelőre, vagy kattintson a jobb gombbal a szerepkör hivatkozás és válassza a **tulajdonságok**, az alábbi ábrán látható módon.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

A szolgáltatásdefiníció és a szolgáltatás konfigurációs fájljait az alapul szolgáló sémák kapcsolatos információkért tekintse meg a [Sémareferenciája](https://msdn.microsoft.com/library/azure/dd179398.aspx). Szolgáltatás konfigurációjával kapcsolatos további információkért lásd: [felhőalapú szolgáltatások konfigurálása](cloud-services/cloud-services-how-to-configure.md).

## <a name="configuring-role-properties"></a>Szerepkör tulajdonságainak konfigurálása
A webes és feldolgozói szerepkörök tulajdonságlapokat hasonlítanak, habár van néhány különbség, jelölt az alábbi szakaszokban található.

Az a **gyorsítótárazását** lapon konfigurálhatja az Azure szolgáltatások gyorsítótárazását.

### <a name="configuration-page"></a>Konfiguráció lap
Az a **konfigurációs** lapon állíthatja be ezeket a tulajdonságokat:

**Példányok**

Állítsa be a **példány** száma a szolgáltatás fusson a szerepkör-példányok száma tulajdonságot.

Állítsa be a **Virtuálisgép-méretet** tulajdonságot **Extra Small**, **kis**, **Közepes**, **nagy**, vagy **Extra nagy**.  További információkért lásd: [Felhőszolgáltatások mérete](cloud-services/cloud-services-sizes-specs.md).

**Indítási műveletet** (csak webes szerepkör)

Ez a tulajdonság adja meg, hogy a Visual Studio minden kell elindítani a webböngészőt a HTTP-végpontokról vagy a HTTPS-végpontnak, vagy mindkét, hibakereső indításakor beállítása.

A HTTPS-végpont lehetőség áll rendelkezésre, csak akkor, ha a HTTPS-végpont már meghatározta a szerepkör. A HTTPS-végpontnak meghatározhatja a **végpontok** tulajdonságlapján.

Ha már hozzáadta a HTTPS-végpontnak, a HTTPS-végpont beállítás alapértelmezés szerint engedélyezve van, és a Visual Studio fog elindít egy böngészőt, ezen a végponton, hibakeresés, mellett a böngészőt a HTTP-végpont indítása. A parancs feltételezi, hogy engedélyezve vannak-e a mindkét indítási lehetőséget.

**Diagnosztika**

A webes szerepkör alapértelmezés szerint engedélyezve van a diagnosztika. Az Azure-felhőbe projekt- és tárolási szolgáltatásfiókot a helyi storage emulator használata van beállítva. Ha készen áll az Azure telepítéséhez, kiválaszthatja a jelentéskészítő gombra (**...** ) frissítésére a tárfiókot az Azure storage a felhőben. A diagnosztikai adatok vihetők át a tárolási fiók, az igény szerinti vagy automatikusan rendszeres időközönként. Az Azure diagnostics kapcsolatos további információkért lásd: [diagnosztika engedélyezésével az Azure Cloud Services és a virtuális gépek](cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Beállítások lap
Az a **beállítások** lap, a szolgáltatás konfigurációs beállítások is hozzáadhat. Konfigurációs beállítások olyan név-érték párok. A szerepkör futó elolvashatják által biztosított osztályokat futásidőben a konfigurációs beállítások értékének a [Azure felügyelt kódtár](http://go.microsoft.com/fwlink?LinkID=171026). Pontosabban a [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) metódus egy elnevezett konfigurációs beállítás futásidőben értékét adja vissza.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>A tárfiók kapcsolati karakterláncának konfigurálása
A kapcsolati karakterlánc egy konfigurációs beállítás, a storage Emulator vagy az Azure-tárfiók kapcsolati és hitelesítési adatokat biztosító. Amikor a kódot kell érheti el az Azure storage szolgáltatások adatait – Ez azt jelenti, hogy a blob, a várólista vagy a tábla adatai – egy szerepkörben futó, akkor határozza meg, hogy a tárfiók kapcsolati karakterláncot.

A kapcsolati karakterlánc, amely egy Azure-tárfiókra mutat egy meghatározott formátumot kell követnie. Kapcsolati karakterláncok létrehozásával kapcsolatos további információkért lásd: [konfigurálása Azure Storage kapcsolati karakterláncok](storage/common/storage-configure-connection-string.md).

Amikor készen áll az Azure storage szolgáltatásainak szemben a szolgáltatás tesztelése, vagy amikor készen áll a felhőalapú szolgáltatás üzembe helyezése az Azure-ba, módosíthatja bármilyen kapcsolati karakterláncok az Azure storage-fiók mutasson értékét. Válassza ki (**...** ) elemre, jelölje be **adja meg a tárfiók hitelesítő adatainak**. Adja meg a fiók adatait, amely tartalmazza a fiók nevét és a fiókkulcsot. Az a **tárolási fiók kapcsolati karakterlánc** párbeszédpanelen adhatja is meg, hogy kívánja-e az alapértelmezett HTTPS-végpontnak (az alapértelmezett beállítás), a HTTP-végpontokról alapértelmezett vagy egyéni végpontokat. Előfordulhat, hogy kívánja használni az egyéni végpontok Ha egy egyéni tartománynevet a szolgáltatásra regisztrált leírtak [blob adatok egyéni tartománynév beállítása az Azure-tárfiók](storage/blobs/storage-custom-domain-name.md).

> [!IMPORTANT]
> Módosítania kell a kapcsolati karakterláncok az Azure-tárfiók mutassanak, a szolgáltatás telepítése előtt. Ha ezt elmulasztja, ez a szerepkör nem fog elindulni, vagy a inicializálása, foglalt és leállítása állapota okozhat.
> 
> 

## <a name="endpoints-page"></a>Végpontok lap
A feldolgozói szerepkör több HTTP, HTTPS vagy TCP-végpontok is rendelkezhetnek. Végpontok lehetnek a bemeneti végpontok, amelyek külső ügyfelek számára elérhető, vagy a belső végpontok, a szolgáltatásban futó egyéb szerepkörök számára elérhető.

* Egy HTTP-végpont elérhető külső ügyfelek és a böngészők, módosítsa a végpont típusát a felhasználótól, és adjon meg egy nevet és egy nyilvános port számát.
* Ha HTTPS-végpontnak szeretné elérhetővé tenni külső ügyfelek és a böngészők, a végpont típusának módosítása **bemeneti**, és adjon meg egy nevet, egy nyilvános port számát és a felügyeleti tanúsítvány nevét.
  
    Vegye figyelembe, hogy előtt meg lehet adni egy felügyeleti tanúsítványt meg kell adni a tanúsítvány a a **tanúsítványok** tulajdonságlapján.
* A végpont belső hozzáférés elérhetővé teszi a felhőszolgáltatásban található más szerepkörök, belső végpont típusának módosítása, és adja meg egy nevet és a lehetséges titkos portok ezen a végponton.

## <a name="local-storage-page"></a>Helyi tároló lap
Használhatja a **helyi tároló** tulajdonságlapján egy vagy több szerepkör helyi tároló-erőforrásokat lefoglalni. A helyi tároló egyik erőforrásához egy fenntartott könyvtárat az Azure virtuális gép szerepkör példánya fut. a fájlrendszerben.

## <a name="certificates-page"></a>Tanúsítványok lap
Az a **tanúsítványok** lapon tanúsítványok a szerepkörhöz is hozzárendelhető. A hozzáadott tanúsítványok segítségével konfigurálja a HTTPS-végpontnak a **végpontok** tulajdonságlapján.

A **tanúsítványok** tulajdonságlapján hozzáadja a tanúsítványokkal kapcsolatos adatokat a szolgáltatás konfigurációját. Vegye figyelembe, hogy a tanúsítványok nem vannak csomagolva az szolgáltatással; fel kell tölteni a tanúsítványok külön Azure keresztül a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885).

Egy tanúsítványt a szerepkörrel társítani, adja meg a tanúsítvány nevét. Ez a név segítségével tekintse meg a tanúsítványt a HTTPS-végpontnak konfigurálásakor a **végpontok** tulajdonságlapján. Ezt követően adja meg, hogy a tanúsítványtároló **helyi számítógép** vagy **aktuális felhasználó** és a tároló neve. Végül adja meg a tanúsítvány ujjlenyomatát. Ha a tanúsítvány a jelenlegi User\Personal (a) tároló, a a tanúsítvány ujjlenyomata a tanúsítvány kijelölésével adatokat tartalmazó listát is megadhatja. Bármely más helyen található, adja meg kézzel az ujjlenyomat értékét.

Amikor egy tanúsítványt a tanúsítványtárolóból, összes köztes tanúsítvány automatikusan a konfigurációs beállításokat. A köztes tanúsítványok is fel kell tölteni, az Azure-bA a szolgáltatás az SSL megfelelő konfigurálásához.

Bármely felügyeleti tanúsítványok társítani a szolgáltatás csak akkor, amikor a felhőben fut a szolgáltatás vonatkozik. Ha a szolgáltatás fut a helyi fejlesztési környezetben, azt a compute emulator által kezelt szabványos tanúsítványt használ.

## <a name="configuring-the-azure-cloud-service-project"></a>Az Azure-felhőszolgáltatás-projekt konfigurálása
Egy teljes Azure-felhőszolgáltatás-projekt vonatkozó beállítások konfigurálásához először megnyitja a helyi menü az adott projekt csomópont, és majd kattintson a Tulajdonságok parancsra nyissa meg a tulajdonságait tartalmazó oldalakon. Az alábbi táblázat a weblapokat meglátogató tulajdonság.

| Tulajdonságlap. | Leírás |
| --- | --- |
| Alkalmazás |Ezen a lapon, amely a felhőszolgáltatás-projekt használja, és frissítheti az eszközök aktuális verziójának Azure eszközök verziója információt jeleníthet meg. |
| Események létrehozása |Ezen a lapon megadhatja előtti és utáni események. |
| Fejlesztés |Ezen a lapon adhat meg build-konfigurációs utasításokról és a feltételeket, amely alatt a utáni eseményeket futnak. |
| Web |Ezen a lapon konfigurálhatja a webkiszolgálóra vonatkozó beállításokat. |

