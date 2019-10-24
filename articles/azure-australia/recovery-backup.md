---
title: Biztonsági mentés és vész-helyreállítás az Azure Ausztráliában
description: Biztonsági mentési és vész-helyreállítási Microsoft Azure az ausztráliai kormányzati szervek számára, mivel az az ASD Essential 8-hoz kapcsolódik
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 078918cbfeffb1ff5f3626b5add96bc6622a90a7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571522"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Biztonsági mentés és vész-helyreállítás az Azure Ausztráliában

A biztonsági mentési és vész-helyreállítási tervek az összes szervezet számára kritikus fontosságúak. A biztonsági mentési megoldás fontosságát az [ausztrál Cyber Security Center Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm)-ban való bevonásával emeljük ki.

Microsoft Azure két olyan szolgáltatást biztosít, amelyek lehetővé teszik a rugalmasságot: Azure Backup és Azure Site Recovery. Ezek a szolgáltatások lehetővé teszik a helyszíni és a Felhőbeli adatok védelme különböző tervezési forgatókönyvek esetén. Azure Backup és Azure Site Recovery mindkettő közös tárolási és felügyeleti erőforrást használ: az Azure Recovery Services-tárolót. Ez a tároló a Azure Backup-és Azure Site Recovery-adatkezeléshez, figyeléshez és elkülönítéshez használatos.

Ez a cikk részletesen ismerteti a Azure Backup és Azure Site Recovery megvalósításának legfontosabb kialakítási elemeit az [ausztrál Signals Igazgatósága (ASD) adatbiztonsági kézi (ISM) vezérlőelemeinek](https://acsc.gov.au/infosec/ism/index.htm).

## <a name="azure-backup"></a>Azure Backup

![Azure Backup](media/backup-overview.png)

Azure Backup a hagyományos helyszíni biztonsági mentési megoldáshoz hasonlít, és lehetővé teszi mind a helyszíni, mind az Azure által üzemeltetett adatbiztonsági mentést. A Azure Backup a következő adattípusok biztonsági mentésére használható az Azure-ba:

* Fájlok és mappák
* Támogatott Windows-és Linux-alapú operációs rendszerek:
  * Hyper-V és VMWare hypervisorok
  * Fizikai hardver
* Támogatott Microsoft-alkalmazások

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery replikálja azokat a munkaterheléseket, amelyek egyetlen virtuális gépről vagy többrétegű alkalmazásokból állnak. A replikációt a helyszíni környezetből az Azure-ba, az Azure-régiók között, illetve a Azure Site Recovery által összehangolt helyi telephelyek között lehet támogatni. A helyszíni virtuális gépek az Azure-ba vagy egy támogatott helyszíni hypervisorba replikálhatók. A konfigurálást követően Azure Site Recovery összehangolja a replikációt, a feladatátvételt és a feladatátvételt.

## <a name="key-design-considerations"></a>A legfontosabb tervezési szempontok

Biztonsági mentési vagy vész-helyreállítási megoldás megvalósításakor a javasolt megoldásnak a következőket kell figyelembe vennie:

* A rögzítendő adattárolás hatóköre és mennyisége
* Az adat megőrzési ideje
* Az adattárolás biztonságos tárolása és kezelése
* A földrajzi hely, ahol az adattároló található
* Rutin rendszertesztelési eljárások

Az ISM útmutatást nyújt azokról a biztonsági megfontolásokról, amelyeket a megoldás tervezésekor kell elvégezni. A Microsoft Azure a biztonsági megfontolások megoldását teszi lehetővé.

### <a name="data-sovereignty"></a>Adatszuverenitás

A szervezeteknek biztosítaniuk kell, hogy az adatszuverenitás a felhőalapú tárolóhelyek használata esetén is fennmaradjon. Azure Policy lehetővé teszi az Azure-erőforrások létrehozásának engedélyezett helyeinek korlátozását. Az "engedélyezett helyszínek" beépített Azure Policy használatával biztosítható, hogy a hozzárendelt Azure Policy hatókörében létrehozott Azure-erőforrások csak a kijelölt földrajzi helyekről jöjjenek létre.

Az Azure-erőforrások földrajzi korlátozásának Azure Policy elemei a következők:

* allowedLocations
* allowedSingleLocation

Ezek a házirendek lehetővé teszik az Azure-rendszergazdák számára, hogy korlátozzák a létrehozást a kijelölt helyek listájára vagy akár egy földrajzi helyre.

### <a name="redundant-and-geographically-dispersed-storage"></a>Redundáns és földrajzilag szétszórt tároló

Az Azure Recovery Service-tárolóban tárolt adattárakat a rendszer mindig redundáns tárterületen tárolja. Alapértelmezés szerint a helyreállítási tár az Azure földrajzilag redundáns tárolást (GRS) használja. A GRS használatával tárolt adattárakat a rendszer a helyreállítási tár [másodlagos párosított régiójában](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)lévő többi Azure-adatközpontba replikálja. A replikált adatai írásvédettként tárolódnak, és csak akkor írhatók, ha van Azure-beli feladatátvételi esemény. Az Azure-adatközpontban az adatmennyiséget külön tartalék tartományok és frissítési tartományok között replikálja a hardver vagy a karbantartás-alapú leállás valószínűségének minimalizálása érdekében. A GRS évente legalább 99.99999999999999%-os rendelkezésre állást biztosít.

Az Azure Recovery Services-tároló beállítható helyileg redundáns tárolás (LRS) használatára. A LRS egy alacsonyabb díjszabású tárolási lehetőség, amely csökkenti a rendelkezésre állást. Ez a redundancia-modell ugyanazt a replikálást alkalmazza a különálló tartalék tartományok és a frissítési tartományok között, de nem replikálódnak a földrajzi régiók között. A LRS-tárolón található, ugyanakkor a GRS-ként nem rugalmasként tárolt adatmennyiség évente legalább 99,999999999%-os rendelkezésre állást biztosít.

A hagyományos külső tárolási technológiáktól, például a szalagos adathordozótól eltérően az adatok további példányai automatikusan létrejönnek, és nem igényelnek további adminisztratív terhelést.

### <a name="restricted-access-and-activity-monitoring"></a>Korlátozott hozzáférés és tevékenységek figyelése

A biztonsági mentési adatoknak meg kell védeniük a sérülést, a módosítást és a nem jóváhagyott törlést. Mind a Azure Backup, mind a Azure Site Recovery a közös Azure felügyeleti háló használata. Ez a háló részletes naplózást, naplózást és szerepköralapú Access Control (RBAC) biztosít az Azure-ban található erőforrásokhoz. A biztonsági mentési információhoz való hozzáférés korlátozható a felügyeleti munkatársak kiválasztására, és a biztonsági mentési adatgyűjtést érintő műveletek naplózása és naplózása is lehetséges.

A Azure Backup és Azure Site Recovery beépített naplózási és jelentéskészítési funkciókkal is rendelkezik. A biztonsági mentés vagy a replikálás során felmerülő problémákat az Azure felügyeleti háló használatával jelentik a rendszergazdáknak.

Az Azure Recovery Services-tároló a következő további adatbiztonsági intézkedéseket is tartalmazta:

* A biztonsági mentési adat a törlési művelet után 14 nappal megmarad.
* Riasztások és értesítések a kritikus műveletekhez, például a "biztonsági mentés leállítása az adatok törlésével"
* A kritikus műveletekre vonatkozó biztonsági PIN-követelmények
* A minimális megőrzési időtartam ellenőrzése folyamatban van.

A minimális megőrzési időtartam ellenőrzései a következők:

* A napi megőrzéshez legalább hét nap megőrzési időt kell kiszolgálni
* Heti megőrzéshez legalább négy héttel a megőrzéshez
* A havi megőrzéshez legalább három hónapos megőrzési időt kell megtartani
* Az évenkénti megőrzéshez legalább egy évig megőrzés

Az Azure-ban tárolt biztonsági mentési adatok az Azure Storage Service Encryption (SSE) használatával titkosítva vannak. Ez alapértelmezés szerint engedélyezve van minden új és meglévő Storage-fiók esetében, és nem tiltható le. A rendszer a beolvasás során automatikusan visszafejti a titkosított adatok visszafejtését. Alapértelmezés szerint az SSE használatával titkosított adattitkosítás a Microsoft által biztosított és felügyelt kulccsal van titkosítva. A szervezetek dönthetnek úgy, hogy saját titkosítási kulcsot biztosítanak és kezelhetnek az SSE-vel való használatra. Ez egy opcionális kiegészítő biztonsági réteget biztosít a titkosított információk számára. Ezt a kulcsot az ügyfél helyi vagy biztonságos módon tárolhatja az Azure Key vaulton belül.

### <a name="secure-data-transport"></a>Biztonságos adatátviteli szolgáltatás

Azure Backup az AES 256 használatával titkosított adatátvitel az átvitel során. Ezeket az adatvédelmet a felügyeleti munkatársak által a biztonsági mentés első konfigurálásakor létrehozott jelszó használatával biztosítjuk. A Microsoft nem fér hozzá ehhez a jelszóhoz, ami azt jelenti, hogy az ügyfélnek biztosítania kell, hogy ezt a jelszót biztonságosan tárolja. Ezután az adatátvitel biztonságos HTTPS-kapcsolaton keresztül történik a helyszíni környezet és az Azure Recovery Services-tároló között.  Ezután a Recovery Services-tárolóban lévő adatok titkosítva maradnak az Azure SSE használatával.

A Azure Site Recovery az adatátvitelt is mindig titkosítja. Az összes replikált adatforgalom biztonságosan az Azure-ba kerül a HTTPS és a TLS használatával. Amikor egy Azure-ügyfél ExpressRoute-kapcsolaton keresztül csatlakozik az Azure-hoz, Azure Site Recovery az adatküldés ezen a privát kapcsolaton keresztül történik.  Ha egy Azure-ügyfél VPN-kapcsolaton keresztül csatlakozik az Azure-hoz, az adat a helyszíni és a Recovery Services-tároló között biztonságosan az interneten keresztül replikálódik.

Ez a biztonságos hálózati adatforgalom eltávolítja a biztonsági kockázatokat és a mérséklési követelményeket a hagyományos külső biztonsági mentési tárolási megoldások, például a szalagos adathordozók kezeléséhez.

### <a name="data-retention-periods"></a>Adatmegőrzési időszakok

Legalább három hónapos biztonsági mentési megőrzési időszak ajánlott, azonban a hosszabb megőrzési időszakokat általában kötelező megadni. Azure Backup legfeljebb 9999 másolatot készíthet egy biztonsági mentésről. Ha a védett példányok egyetlen Azure Backupét naponta megtartották, ez a napi 27 éves biztonsági mentés megtartását teszi lehetővé. A védett példányok havi biztonsági mentései 833 éves megőrzést tesznek lehetővé. Mivel a biztonsági mentési adatok elavultak, és az idő múlásával kevesebb részletes biztonsági mentést tartanak fenn, a biztonsági mentési adatok teljes megőrzési időszaka növekszik.  Az Azure nem korlátozza, hogy az adatmennyiség az Azure Recovery Services-tárolóban maradjon, csak a biztonsági másolatok teljes száma. A régi vagy az új biztonsági másolatok visszaállítása között nem áll fenn teljesítménybeli különbség, ezért minden visszaállítás ugyanannyi időt vesz igénybe.

Az Azure Recovery Services-tárolóban számos alapértelmezett biztonsági mentési és adatmegőrzési szabályzat van érvényben.  A rendszergazdák egyéni biztonsági mentési és adatmegőrzési szabályzatokat is létrehozhatnak.

![Azure Backup házirend](media/create-policy.png)

Azure Backup és adatmegőrzési szabályzatok konfigurálásakor egyensúlyt kell találni a biztonsági mentés gyakorisága és a hosszú távú megőrzési követelmények között.

### <a name="backup-and-restore-testing"></a>Tesztelés biztonsági mentése és visszaállítása

Az ISM a biztonsági mentési adatai tesztelését javasolja annak biztosítására, hogy a védett adatai érvényesek legyenek, amikor visszaállításra vagy feladatátvételre van szükség. A Azure Backup és Azure Site Recovery lehetővé teszi a védett adattárolás tesztelését a biztonsági mentés vagy a replikálása után is. Az Azure Backup által felügyelt adatmennyiség visszaállítható egy kijelölt helyre, és az adatkonzisztencia ellenőrizhető.

Azure Site Recovery beépített képességgel rendelkezik a feladatátvételi teszt végrehajtásához. Az Recovery Services-tárolóba replikált munkaterhelések visszaállíthatók egy kijelölt Azure-környezetbe. A cél-visszaállítási környezet minden éles környezetből teljesen elkülöníthető, így biztosítva, hogy a teszt végrehajtása közben ne legyen hatással az éles rendszerekre. A teszt befejezése után a tesztelési környezet és az összes erőforrás azonnal törölhető a működési költségek csökkentése érdekében.

A feladatátvétel tesztelése és érvényesítése automatizálható az Azure platformon beépített Azure Automation szolgáltatás használatával. Ez lehetővé teszi, hogy a feladatátvételi teszt ütemezése automatikusan megtörténjen, hogy az Azure-ba történő replikáció sikeres legyen.

## <a name="next-steps"></a>További lépések

Tekintse át a [Azure Policy biztonságának biztosításáról](azure-policy.md)szóló cikket.
