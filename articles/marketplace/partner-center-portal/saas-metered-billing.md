---
title: Mért számlázás a Marketplace-mérési szolgáltatás használatával | Azure piactér
description: Ez a dokumentáció az SaaS-ajánlatok rugalmas számlázási modellel történő közzétételének útmutatója.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a244e4f7ac406fecd4c053b39a5e9a9cb6ecab2c
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105493"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Mért számlázás a Marketplace-mérési szolgáltatás használatával

A Marketplace-mérő szolgáltatással a kereskedelmi piactér programban a nem standard egységek alapján felszámított szoftveres (SaaS) ajánlatokat hozhat létre.  Az ajánlat közzététele előtt meg kell határoznia a számlázási dimenziókat, például a sávszélességet, a jegyeket vagy a feldolgozott e-maileket.  Az ügyfelek ezt a dimenziók fogyasztása alapján fizetik be, és a rendszer a Microsoftot a piactér-mérési szolgáltatás API-ját a számlázó események során felhasználva értesíti.  

## <a name="prerequisites-for-metered-billing"></a>A mért számlázás előfeltételei

Ahhoz, hogy egy SaaS-ajánlat mért számlázást használjon, a következőket kell tennie:

* Ismerje meg a [Microsoft ajánlaton keresztüli értékesítésre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) vonatkozó összes követelményt az SaaS- [ajánlat létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)című részben leírtak szerint.
* Integrálhatja a [SaaS-megvalósítási API-kat](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) az ügyfeleknek az ajánlat kiépítéséhez és az azokhoz való kapcsolódáshoz.  
* Az **átalánydíjas** díjszabási modellhez kell beállítani a szolgáltatás ügyfeleinek kitöltéséhez.  A méretek az átalánydíjas árképzési modell opcionális kiterjesztése. 
* Integrálja a [piactér-mérési szolgáltatás API](./marketplace-metering-service-apis.md) -kkal, hogy tájékoztassa a Microsoftot a számlázható eseményekről.

>[!Note]
>A piactér-mérési szolgáltatás csak az átalány számlázási modellben érhető el, és nem vonatkozik a felhasználónkénti számlázási modellre.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hogyan illeszkedik a mért számlázás a díjszabáshoz

Ha az ajánlatot az árképzési modelljeivel együtt határozza meg, fontos megérteni az ajánlat-hierarchiát.

* Minden SaaS-ajánlat úgy van konfigurálva, hogy a Microsofton keresztül értékesítsen vagy sem.  Ez a beállítás az ajánlat közzétételekor nem módosítható.
* A Microsofton keresztüli értékesítésre konfigurált SaaS-ajánlatok egy vagy több csomaggal rendelkezhetnek. A felhasználók a SaaS-ajánlatra fizetnek, de a Microsoft a csomag keretében vásárolható meg.
* Minden csomaghoz hozzá van rendelve egy díjszabási modell: **átalánydíjas** vagy **felhasználónként**. Az ajánlat minden csomagját ugyanahhoz az árképzési modellhez kell társítani. Például nem lehet olyan ajánlat, amelyben az egyik terve átalánydíjas díjszabási modell, a másik pedig felhasználónkénti díjszabási modell.
* Az átalánydíjas számlázási modellhez konfigurált minden egyes csomagon belül legalább egy ismétlődő díj szerepel (amely $0):
    * Ismétlődő **havi** díj: a csomag megvásárlásakor a rendszer havi ismétlődéssel előre fizetett átalányösszegű havi díjat számít fel.
    * Ismétlődő **éves** díj: a csomag megvásárlásakor a felhasználó éves ismétlődéssel előre fizetett átalányösszegű éves díj.
* Az ismétlődő díjak mellett a terv olyan választható dimenziókat is tartalmazhat, amelyeket az ügyfelek az átalányban nem szereplő használati díj ellenében használhatnak fel.   Az egyes dimenziók egy számlázható egységet jelölnek, amelyet a szolgáltatás kommunikál a Microsofttal a [Marketplace-mérési szolgáltatás API-ja](./marketplace-metering-service-apis.md)használatával.

## <a name="sample-offer"></a>Minta ajánlat

A contoso például egy contoso Notification Services (CNS) nevű SaaS-szolgáltatással rendelkező közzétevő. A CNS lehetővé teszi, hogy az ügyfelek e-mailben vagy szöveges üzenetben küldjenek értesítéseket. A contoso közzétevőként van regisztrálva az Azure-ügyfelek számára elérhető kereskedelmi piactér program keretében.  A következő két terv van társítva a CNS-hez:

* Alapcsomag
    * 10000-os e-mailek és 1000-szövegek küldése 0/hónapra
    * Az 10000-es e-mailek után fizessen $1 minden egyes 100 e-mailhez
    * Az 1000-es szövegeken kívül minden szövegért fizessen $0,02
* Prémium szintű csomag
    * 50000-os e-mailek és 10000-es szövegek elküldése 350/hó-ig
    * Az 50000-es e-mailek után fizessen $0,5 minden egyes 100 e-mailhez
    * Az 10000-es szövegeken kívül minden szövegért fizessen $0,01

A CNS szolgáltatásra előfizető Azure-ügyfelek a kiválasztott csomag alapján havonta elküldhetik a benne foglalt mennyiségű szöveget és e-maileket.  A contoso a használati események a Microsoftnak való elküldése nélkül méri a használatot a befoglalt mennyiséggel.  Ha az ügyfelek a benne foglalt mennyiségnél többet használnak, nem kell megváltoztatniuk a csomagokat, vagy bármi mást elvégezniük.  A contoso a befoglalt mennyiségen felül méri a túlterhelést, és megkezdi a használati események kibocsátását a Microsoftnak a [Piactéri mérési szolgáltatás API](./marketplace-metering-service-apis.md)használatával történő további használat érdekében.  A Microsoft ezt követően a kiadó által megadott további használatért díjat számít fel.

## <a name="billing-dimensions"></a>Számlázási méretek

A számlázási dimenziók arra szolgálnak, hogy tájékoztassák az ügyfelet arról, hogy a szoftver használatára hogyan lesznek felszámítva, valamint a használati események a Microsoftnak való közlésére is. Ezek az alábbiak szerint vannak meghatározva:

* **Dimenzió azonosítója**: a használati események kibocsátása közben megváltoztathatatlan azonosító.
* **Dimenzió neve**: a dimenzióhoz társított megjelenítendő név, például "szöveges üzenetek elküldve".
* **Mértékegység**: a számlázási egység leírása, például "szöveges üzenet" vagy "per 100 e-mail".
* **Díj/egység**: a dimenzió egy egységének díja.  
* **Belefoglalt mennyiség a havi időszakra**: az ismétlődő havi díjat fizető ügyfelek számára havi dimenzióban szereplő mennyiségnek egész számnak kell lennie.
* **Belefoglalt mennyiség az éves időszakra**: a havi dimenzió mennyisége az ismétlődő éves díjat fizető ügyfelek esetében egész számnak kell lennie.

A számlázási dimenziók az ajánlat minden csomagjában meg vannak osztva.  Egyes attribútumok a dimenzióra vonatkoznak az összes csomagra, és más attribútumok is megtervezve.

A dimenziót definiáló attribútumok az ajánlat minden csomagjában meg vannak osztva.  Mielőtt közzéteszi az ajánlatot, az ezen attribútumok bármely csomag kontextusában történt módosítása hatással lesz az összes csomag dimenziójának meghatározására.  Miután közzétette az ajánlatot, ezek az attribútumok többé nem lesznek szerkeszthetve.  Ezek az attribútumok a következők:

* azonosító
* Name (Név)
* Mértékegység

A dimenziók egyéb attribútumai az egyes csomagokra jellemzőek, és a tervtől eltérő értékekkel rendelkezhetnek.  A terv közzététele előtt módosíthatja ezeket az értékeket, és csak ezt a csomagot fogja érinteni.  Miután közzétette a csomagot, ezek az attribútumok többé nem lesznek szerkeszthetve.  Ezek az attribútumok a következők:

* Ár / egység
* A havi ügyfelek belefoglalt mennyisége 
* Befoglalt mennyiség az éves ügyfelek számára 

A méretek két speciális fogalmat is tartalmazhatnak, amelyek "engedélyezve" és "végtelen":

* Az **engedélyezve** érték azt jelzi, hogy ez a csomag részt vesz ebben a dimenzióban.  Ha olyan új csomagot hoz létre, amely nem küldi el ezt a dimenziót, érdemes elhagynia ezt az eltávolítást.  Emellett a csomag első közzététele után hozzáadott új dimenziók a már közzétett tervben "nem engedélyezettként" jelennek meg.  A kikapcsolt dimenziók nem jelennek meg az ügyfelek által látott csomag dimenzióinak listájában.
* A **végtelen**, amelyet a "∞" végtelen szimbólum jelöl, azt jelzi, hogy ez a csomag részt vesz ebben a dimenzióban, de nem számítja fel a használatot ezen a dimenzión.  Ha meg szeretné jeleníteni ügyfelei számára, hogy a dimenzió által képviselt funkciók beletartoznak a csomagba, de a használat korlátozás nélkül.  A végtelen használatú dimenziók az ügyfelek által látott csomag dimenzióinak listájában jelennek meg, ami azt jelzi, hogy a csomagnak soha nem kell díjat fizetnie.

>[!Note] 
>A következő forgatókönyvek explicit módon támogatottak: <br> – Új dimenziót adhat hozzá egy új tervhez.  Az új dimenzió nem lesz engedélyezve a már közzétett csomagok esetében. <br> – Bármilyen dimenzió nélkül közzétehet egy **átalánydíjas** csomagot, majd hozzáadhat egy új tervet, és konfigurálhat egy új dimenziót az adott tervhez. Az új dimenzió nem lesz engedélyezve a már közzétett tervekhez.

## <a name="constraints"></a>Megkötések

### <a name="trial-behavior"></a>Próbaverziós viselkedés

A Piactéri mérési szolgáltatás használatával mért számlázás nem kompatibilis az ingyenes próbaverzióval.  Nem lehet beállítani egy tervet a mért számlázás és az ingyenes próbaverzió használatára.

### <a name="locking-behavior"></a>Zárolási viselkedés

Mivel a piactér-mérési szolgáltatáshoz használt dimenzió azt jelenti, hogy az ügyfél hogyan fizet a szolgáltatásért, a dimenziók összes részletét már nem szerkesztheti a közzététel után.  A közzététel előtt fontos, hogy a méretek teljes mértékben meg legyenek határozva a tervekhez.
  
Ha egy ajánlatot közzétesznek egy dimenzióval, az adott dimenzióra vonatkozó ajánlati szintű adatok már nem módosíthatók:

* azonosító
* Name (Név)
* Mértékegység

A csomag közzététele után a terv szintű részletek már nem módosíthatók:

* Ár / egység
* Belefoglalt mennyiség a havi időszakra
* Belefoglalt mennyiség az éves időszakra
* Azt határozza meg, hogy a dimenzió engedélyezve van-e a csomaghoz

### <a name="upper-limits"></a>Felső határértékek

Az egyetlen ajánlathoz konfigurálható méretek maximális száma 18 egyedi dimenzió lehet.

## <a name="get-support"></a>Támogatás kérése

Ha rendelkezik a következők valamelyikével, nyisson meg egy támogatási jegyet.

* Technikai problémák a Marketplace-mérési szolgáltatás API-jával.
* Egy olyan problémát, amelyet az oldal hibája vagy hibája miatt meg kell adni (pl. helytelen használati esemény).
* A mért számlázással kapcsolatos egyéb problémák. 

A támogatási jegy beküldéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [támogatási lapot](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). Az első néhány legördülő menüt automatikusan kitölti a rendszer. A piactér támogatásához azonosítsa a termékcsaládot **felhő-és online szolgáltatásként**, a termékként a **piactér**-közzétevőként.  Ne módosítsa a legördülő menü előre megadott beállításait.
2. A "termék verziójának kiválasztása" területen válassza az **élő ajánlat kezelése**lehetőséget.
3. A "válasszon ki egy kategóriát, amely a legjobban leírja a problémát" területen válassza az **SaaS-alkalmazások**lehetőséget.
4. A "probléma leírása, amely a legjobban leírja a problémát" területen válassza a **mért számlázás**lehetőséget.
5. A Next ( **tovább** ) gombra kattintva átirányítjuk a **probléma részletei** lapra, ahol további részleteket adhat meg a hibáról.

További közzétevői támogatási lehetőségekért lásd: [a kereskedelmi piactér program támogatása a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) .

## <a name="next-steps"></a>További lépések

- További információért lásd: [Marketplace-mérési szolgáltatás API](./marketplace-metering-service-apis.md) -k.
