---
title: Forgalmi díjas számlázás a piactéri mérési szolgáltatás használatával | Azure Piactér
description: Ez a dokumentáció útmutató a rugalmas számlázási modellekkel rendelkező SaaS-ajánlatokat közzétenni közzétanak független szoftverszállítók számára.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e5a4813301cbab16d1cffabaaa60688f6e826ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281323"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Forgalmi díjas számlázás a piactéri mérési szolgáltatás használatával

A Marketplace-mérési szolgáltatással szoftver-szolgáltatásként (SaaS) kínál a kereskedelmi piactér programban, amely nem szabványos egységek szerint terheli.  Az ajánlat közzététele előtt meg kell határoznia a számlázási dimenziókat, például a sávszélességet, a jegyeket vagy a feldolgozott e-maileket.  Az ügyfelek ezután az ilyen dimenziók fogyasztása szerint fizetnek, és a rendszer tájékoztatja a Microsoftot a Marketplace-mérési szolgáltatás API-ján keresztül a számlázható események előfordulásakor.  

## <a name="prerequisites-for-metered-billing"></a>A forgalmi díjas számlázás előfeltételei

Ahhoz, hogy egy SaaS-ajánlat forgalmi díjas számlázást használhasson, a következőket kell tennie:

* Feleljen meg a [Microsoft-ajánlaton keresztüli értékesítésre](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) vonatkozó összes ajánlati követelménynek a [SaaS-ajánlat létrehozása című](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)dokumentumban ismertetett módon.
* Integrálja a [SaaS teljesítési API-k](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) az ügyfelek számára, hogy kiépítése és csatlakozzon az ajánlathoz.  
* Konfigurálni kell az **átalánydíjas** árképzési modellt a szolgáltatásért az ügyfelek számára.  A méretek az átalánydíjas árképzési modell opcionális kiterjesztései. 
* Integrálja a [Piactér mérési szolgáltatás API-k,](./marketplace-metering-service-apis.md) hogy tájékoztassa a Microsoftot a számlázható események.

>[!Note]
>Marketplace-mérési szolgáltatás csak az átalánydíjas számlázási modell, és nem vonatkozik a felhasználónkénti számlázási modell.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Hogyan illeszkedik a forgalmi díjas számlázás az árképzéshez?

Amikor az ajánlat meghatározásáról és a díjszabási modellekről van szó, fontos megérteni az ajánlathierarchiát.

* Minden SaaS-ajánlat úgy van konfigurálva, hogy a Microsofton keresztül vagy sem értékesítsen.  Ez a beállítás nem módosítható az ajánlat közzététele után.
* Minden SaaS-ajánlat, amely a Microsofton keresztül történő értékesítésre van konfigurálva, egy vagy több csomagkal rendelkezhet. A felhasználó előfizet a SaaS-ajánlatra, de a Microsofton keresztül vásárolják meg egy csomag keretében.
* Minden csomaghoz tartozik egy árképzési modell: **átalánydíjas** vagy **felhasználónkénti**. Az ajánlatban lévő összes csomagnak ugyanahhoz a díjszabási modellhez kell társítania. Például nem lehet olyan ajánlat, ahol az egyik csomag jattsa átalánydíjas árképzési modell, a másik pedig egy felhasználó díjszabási modell.
* Az átalánydíjas számlázási modellhez konfigurált minden csomagon belül legalább egy ismétlődő díj (amely 000 000.) tartalmazza:
    * Ismétlődő **havi** díj: havi rendszerességre előre fizetett havi díj, amikor a felhasználó megvásárolja a csomagot.
    * Ismétlődő **éves** díj: átalányéves díj, amelyet a felhasználó a csomag vásárlásakor előre kifizet egy éves ismétlődés után.
* Az ismétlődő díjakon kívül a terv választható dimenziókat is tartalmazhat, amelyek a vevőknek az átalánydíjban nem szereplő használatért történő felszámítására szolgálnak.   Minden dimenzió egy számlázható egységet jelöl, amelyet a szolgáltatás a [Marketplace-mérési szolgáltatás API-jával](./marketplace-metering-service-apis.md)kommunikál a Microsoftnak.

## <a name="sample-offer"></a>Mintaajánlat

A Contoso például egy Contoso Notification Services (CNS) nevű SaaS-szolgáltatásközzétevő. CNS lehetővé teszi az ügyfelek számára, hogy értesítéseket küldjön akár e-mailben vagy szöveges. A Contoso közzétevőként van regisztrálva a Kereskedelmi piactér partnerközpontjában, hogy ajánlatokat tegyen közzé az Azure-ügyfelek számára.  A cns-hez két terv kapcsolódik, amelyeket az alábbiakban ismertetünk:

* Alapterv
    * Küldjön 10000 e-mailt és 1000 sms-t $0/month
    * Az 10000 e-mailen túl fizessen $1-t minden 100 e-mailért
    * Az 1000 sms-en túl minden szövegért 0,02 dollárt kell fizetni
* Prémium szintű csomag
    * Küldjön 50000 e-mailt és 10000 sms-t $ 350/hó
    * Az 50000 e-mailen túl fizessen 0,5 dollárt minden 100 e-mailért
    * Az 10000 sms-en túl minden szövegért 0,01 dollárt kell fizetni

A CNS-szolgáltatásra előfizető Azure-ügyfelek a kiválasztott terv alapján havonta küldhetik el a mellékelt mennyiségű szöveget és e-maileket.  A Contoso a használatot a mellékelt mennyiségig méri anélkül, hogy használati eseményeket küldene a Microsoftnak.  Ha az ügyfelek többet fogyasztanak, mint a benne foglalt mennyiség, nem kell módosítaniuk a terveket, vagy bármi mást kell tenniük.  A Contoso méri a túlterhelést a benne foglalt mennyiségen túl, és használati eseményeket bocsát ki a Microsoft nak további használat érdekében a [Marketplace-mérési szolgáltatás API-jával.](./marketplace-metering-service-apis.md)  A Microsoft pedig a kiadó által meghatározott további használatért díjat számít fel az ügyfélnek.

## <a name="billing-dimensions"></a>Számlázási dimenziók

A számlázási dimenziók arra szolgálnak, hogy kommunikáljanak az ügyféllel arról, hogyan kell fizetniük a szoftver használatáért, valamint a használati eseményeknek a Microsoftnak való közlésére. Ezek meghatározása a következő:

* **Dimenzióazonosító**: a használati események kivezetése közben hivatkozott megváltoztathatatlan azonosító.
* **Dimenzió neve**: a dimenzióhoz társított megjelenítendő név, pl. "elküldött szöveges üzenetek".
* **Mértékegység:** a számlázási egység leírása, pl. "sms-enként" vagy "100 e-mailre".
* **Egységár:** a dimenzió egy egységének ára.  
* **A havi kifejezésre vonatkozó mennyiség:** az ismétlődő havi díjat fizető vevők havi dimenziójának mennyiségének egész számnak kell lennie.
* **Az éves futamidőre vonatkozó mennyiség:** az ismétlődő éves díjat fizető vevők havonta megadott dimenziójának egész számnak kell lennie.

A számlázási dimenziók meg vannak osztva az ajánlat összes csomagja között.  Egyes attribútumok az összes tervdimenzióra vonatkoznak, míg más attribútumok tervspecifikusak.

A dimenziót meghatározó attribútumok meg oszlanak az ajánlat összes tervén.  Az ajánlat közzététele előtt az attribútumok bármely terv környezetéből történő módosítása hatással lesz a dimenziódefinícióra az összes tervben.  Az ajánlat közzététele után ezek az attribútumok a továbbiakban nem szerkeszthetők.  Ezek az attribútumok a következők:

* Azonosító
* Név
* Mértékegység

A dimenzió többi attribútuma az egyes tervekre jellemző, és a tervtől a tervig eltérő értékekkel rendelkezhet.  A terv közzététele előtt módosíthatja ezeket az értékeket, és csak ez a terv lesz hatással.  A terv közzététele után ezek az attribútumok a továbbiakban nem szerkeszthetők.  Ezek az attribútumok a következők:

* Egységár
* A havi vevők hez tartozó mennyiség 
* Az éves vevők reálmennyisége 

A dimenzióknak két speciális fogalma is van: "engedélyezve" és "végtelen":

* **Az Engedélyezve azt** jelzi, hogy a terv részt vesz ebben a dimenzióban.  Ezt nem lehet bejelölve hagyni, ha olyan új csomagot hoz létre, amely nem küld használati eseményeket ezen a dimenzión alapuló.  A terv első közzététele után hozzáadott új dimenziók is "nem engedélyezve" jelennek meg a már közzétett tervben.  A letiltott dimenziók nem jelennek meg az ügyfelek által látott terv dimenzióinak listájában.
* **A Végtelen**, amelyet a végtelen szimbólum "啦" jelöl, azt jelzi, hogy ez a terv részt vesz ebben a dimenzióban, de nem méri a használatot ezzel a dimenzióval szemben.  Ha azt szeretné jelezni a vevőknek, hogy az ez a dimenzió által képviselt funkciók szerepelnek a tervben, de a használat nem korlátozza.  A végtelen használatú dimenziók megjelennek az ügyfelek által látott terv dimenziólistáin, jelezve, hogy soha nem számítfel díjat a tervért.

>[!Note] 
>A következő forgatókönyvek kifejezetten támogatottak: <br> - Új dimenziót adhat egy új tervhez.  Az új dimenzió nem lesz engedélyezve a már közzétett csomagokhoz. <br> - Közzétehet egy **átalányalapú** tervet dimenziók nélkül, majd hozzáadhat egy új tervet, és új dimenziót állíthat be a tervhez. Az új dimenzió nem lesz engedélyezve a már közzétett csomagokhoz.

## <a name="constraints"></a>Korlátozások

### <a name="trial-behavior"></a>Próbaverziós viselkedés

A piactéri mérési szolgáltatás használatával végzett forgalmi díjas számlázás nem kompatibilis az ingyenes próbaverzió felajánlásával.  Nem lehet beállítani egy forgalmi díjas számlázást és egy ingyenes próbaverziót egyaránt.

### <a name="locking-behavior"></a>Zárolási viselkedés

Mivel a Piactér mérési szolgáltatással használt dimenzió annak megértését jelenti, hogy az ügyfél hogyan fog fizetni a szolgáltatásért, a dimenzió összes részlete már nem szerkeszthető, ha közzéteszi.  Fontos, hogy a közzététel előtt teljesen definiálja a dimenziókat egy tervhez.
  
Ha egy ajánlat közzététele dimenzióval történik, az adott dimenzió ajánlatszintű részletei már nem módosíthatók:

* Azonosító
* Név
* Mértékegység

A terv közzététele után a tervszintű részletek már nem módosíthatók:

* Egységár
* Tartalmazza a havi időszak mennyiségét
* Az éves futamidő hez tartozó mennyiség
* Azt jelzi, hogy a dimenzió engedélyezve van-e a tervben

### <a name="upper-limits"></a>Felső határok

Az egyetlen ajánlathoz konfigurálható dimenziók maximális száma 18 egyedi dimenzió.

## <a name="get-support"></a>Támogatás kérése

Ha az alábbiak valamelyikével rendelkezik, megnyithat egy támogatási jegyet.

* Technikai problémák a piactér-mérési szolgáltatás API-val.
* Olyan probléma, amelyet el kell szeszálni egy hiba vagy hiba miatt az Ön oldalán (pl. rossz használati esemény).
* A forgalmi díjas számlázással kapcsolatos egyéb problémák. 

A támogatási jegy elküldéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [támogatási oldalt](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). Az első néhány legördülő menü automatikusan kitöltődik az Ön számára. A Marketplace-támogatás esetén a termékcsaládot **felhőalapú és online szolgáltatásokként,** a terméket **marketplace-kiadóként azonosíthatja.**  Ne módosítsa az előre kitöltött legördülő menük kiválasztásait.
2. A "Válassza ki a termékverziót" területen válassza az **Élő ajánlatkezelés lehetőséget.**
3. A "Válasszon egy kategóriát, amely a legjobban leírja a problémát", válassza az **SaaS-alkalmazások**lehetőséget.
4. A "Válassza ki a problémát legjobban leíró problémát" területen válassza a **forgalmi díjas számlázás**lehetőséget.
5. A **Tovább** gomb kiválasztásával a készülék a **Probléma részletei** oldalra irányítja, ahol további részleteket adhat meg a problémáról.

További megjelenítői támogatási lehetőségekért [lásd: A kereskedelmi piactér támogatása a Partnerközpontban.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)

## <a name="next-steps"></a>További lépések

- További információkért [tekintse meg a Marketplace-mérési szolgáltatás API-jait.](./marketplace-metering-service-apis.md)
