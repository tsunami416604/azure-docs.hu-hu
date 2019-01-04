---
title: A StorSimple 8000 sorozat biztonsági |} A Microsoft Docs
description: Ismerteti a biztonság és adatvédelem a StorSimple szolgáltatás, az eszköz és a helyszíni és felhőbeli adatok védelmére.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 734b0cf9373ea98ab33c06b45ad53b46a3355dd6
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976018"
---
# <a name="storsimple-security-and-data-protection"></a>A StorSimple biztonsági és adatvédelmi

## <a name="overview"></a>Áttekintés

Bárki, aki bevezetni új technológia, különösen akkor, ha a technológiát a bizalmas vagy szellemi tulajdont képező adatokat használatos fő szempont. Mivel kiértékelni a különböző technológiák, meg kell fontolnia, megnövekedett kockázata és költsége adatainak védelme. A Microsoft Azure StorSimple biztosít a biztonsági és adatvédelmi megoldás is adatvédelmet biztosít, annak biztosításához:

* **Bizalmas** – csak az arra jogosult személyek is megtekintheti az adatokat.
* **Integritás** – csak arra jogosult személyek módosíthatják vagy törölhetik az adatokat.

A Microsoft Azure StorSimple megoldás egymással kommunikáló négy fő összetevőből áll:

* **StorSimple-Eszközkezelő szolgáltatás a Microsoft Azure-ban üzemeltetett** – a felügyeleti szolgáltatás, amellyel konfigurálhatja és a StorSimple-eszköz kiépítése.
* **StorSimple-eszköz** – a helyi adatközpontban telepített fizikai eszközt. Összes gazdagép és az ügyfelek adatokat generáló kapcsolódni a StorSimple-eszköz, és az eszköz kezeli az adatokat, és áthelyezi az Azure-felhő megfelelő módon.
* **Az eszközhöz csatlakoztatott ügyfelek /-gazdagépekre** – az ügyfelek az infrastruktúra, amely a StorSimple-eszköz csatlakozik, és a védendő adatok létrehozására.
* **Felhőalapú tároló** – a hely az Azure-felhőben, ahol az adatok tárolása történik.

A következő szakaszok ismertetik a StorSimple biztonsági funkciók, amelyek az egyes összetevők és a rajtuk tárolt adatok védelme érdekében. Az esetleges Microsoft Azure StorSimple biztonsági és a hozzájuk tartozó válaszok kapcsolatos kérdések listáját is tartalmazza.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple-Eszközkezelő szolgáltatás védelme

A StorSimple-Eszközkezelő szolgáltatásban a felügyeleti szolgáltatás a Microsoft Azure-ban üzemeltetett, és minden a StorSimple-eszköz, amely a szervezet közvetített kezelhetők. A StorSimple-Eszközkezelő szolgáltatás hozzáférhet a szervezeti hitelesítő adataival bejelentkezni az Azure Portalon egy webböngészőn keresztül.

A StorSimple-Eszközkezelő szolgáltatáshoz való hozzáférés szükséges, hogy a szervezet rendelkezik-e az Azure-előfizetéssel, amely magában foglalja a StorSimple. Az előfizetés szabályozza, hogy az Azure Portal mely szolgáltatásai érhetők el. Ha a szervezet nem rendelkezik Azure-előfizetéssel, és ha szeretne további információkat olvashat, lásd: [Azure-előfizetésre regisztrál](../active-directory/fundamentals/sign-up-organization.md).

A StorSimple-Eszközkezelő szolgáltatás az Azure-ban üzemel, mert védi az Azure biztonsági szolgáltatásait. A Microsoft Azure által biztosított biztonsági funkciókról a [Microsoft Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/security/) talál további információt.

## <a name="storsimple-device-protection"></a>A StorSimple eszköz védelme

A StorSimple-eszköz azonban egy helyszíni hibrid tárolóeszköz, amely tartalmazza a tartós állapotú meghajtókkal (SSD-kkel) és a merevlemezes (HDD) meghajtók, redundáns vezérlőket és automatikus feladatátvételi funkciókat együtt. A tartományvezérlők kezelheti a tárolási rétegek, elhelyezése jelenleg használt (vagy forró) adatokat a helyi tárolóban (a StorSimple eszköz vagy a helyszíni kiszolgálók) ritkábban használt adatok a felhőbe való áthelyezés közben.

Csak a hitelesített eszközök férhessenek hozzá a StorSimple-Eszközkezelő szolgáltatás Azure-előfizetésében létrehozott csatlakozni a StorSimple. Egy eszköz hitelesítéséhez, regisztrálnia kell azt a StorSimple-Eszközkezelő szolgáltatással azáltal, hogy a szolgáltatás regisztrációs kulcsát. A szolgáltatás regisztrációs kulcsával az Azure Portalon létrehozott 128 bit véletlenszerű kulcsot.

![Szolgáltatásregisztrációs kulcs](./media/storsimple-security/ServiceRegistrationKey.png)

Megtudhatja, hogyan a Szolgáltatásregisztrációs kulcs, lépjen be [2. lépés: A Szolgáltatásregisztrációs kulcs lekérése](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

A szolgáltatás regisztrációs kulcsával egy hosszú kulcs több mint 100 karakternél. Másolja a kulcsot, és mentse azt biztonságos helyre a fájlt, hogy történő hitelesítéséhez szükséges további eszközöket használhatja. Ha a szolgáltatás regisztrációs kulcsát a jelszó elvesztése esetén az első eszköz regisztrálása után létrehozhat egy új kulcsot a StorSimple-Eszközkezelő szolgáltatás. Ez nem érinti a meglévő eszközök működését.

Eszköz regisztrálása után jogkivonatok használatával kommunikálni a Microsoft Azure. A Szolgáltatásregisztrációs kulcsot nem használja az eszköz regisztrálása után.

> [!NOTE]
> Azt javasoljuk, hogy minden használat után a szolgáltatás regisztrációs kulcsának újragenerálása.


## <a name="protect-your-storsimple-solution-via-passwords"></a>A StorSimple megoldás a jelszavak védelme

Jelszavak fontos szempont a számítógép biztonsági és széles körben a StorSimple-megoldásban érdekében győződjön meg arról, hogy az adatok csak a jogosult felhasználók számára is elérhető. A StorSimple lehetővé teszi a következő jelszavak beállítása:

* A StorSimple eszköz rendszergazdai jelszava
* Ellenőrző kérdés Handshake Authentication Protocol (CHAP) kezdeményező és a célként megadott jelszavak
* StorSimple Snapshot Manager jelszava

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell a StorSimple és a StorSimple eszköz rendszergazdai jelszava

Windows PowerShell-bővítménye olyan parancssori felületet, amelyek segítségével kezelheti a StorSimple-eszköz. Storsimple-höz készült Windows PowerShell funkcióval rendelkezik, amelyeket lehetővé teszi, hogy regisztrálja az eszközt, az eszköz a hálózati adapter konfigurálásához, bizonyos típusú frissítések telepítése, az eszköz hibaelhárítása a támogatási munkamenet elérésével, és módosítsa az eszköz állapotát. Storsimple-höz készült Windows PowerShell hozzáférhetnek, az az eszköz soros konzoljához való csatlakozással, vagy a Windows PowerShell-távelérés használatával.

PowerShell távoli eljáráshívás HTTPS vagy HTTP keresztül teheti meg. Ha engedélyezve van a Rendszerfelügyeleti webszolgáltatások HTTPS protokollon, szüksége lesz a távfelügyeleti tanúsítvány letöltéséhez az eszközről, és telepítse azt a távoli ügyfélhez. PowerShell-táveléréssel kapcsolatos további információkért látogasson el [távolról csatlakozhat a StorSimple-eszköz](storsimple-8000-remote-connect.md).

Miután a storsimple-höz készült Windows PowerShell segítségével csatlakozni az eszközhöz, szüksége lesz adja meg, jelentkezzen be az eszközt, az eszköz rendszergazdai jelszava.

![Az eszköz rendszergazdai jelszava](./media/storsimple-security/DeviceAdminPW.png)

Tartsa szem előtt az alábbi gyakorlati tanácsokat:

* Távfelügyelet alapértelmezés szerint ki van kapcsolva. A StorSimple-Eszközkezelő szolgáltatás használatával engedélyezheti. Bevált biztonsági gyakorlat csak a ténylegesen szükséges idő alatt közben távelérési engedélyezni kell.
* Ha megváltoztatja a jelszót, mindenképpen az összes távelérési a felhasználók értesítése, hogy azok nem tapasztalnak egy váratlan kapcsolat megszakadását.
* A StorSimple-Eszközkezelő szolgáltatás nem tudja beolvasni a már meglévő jelszavai: Ez csak alaphelyzetbe állíthatja őket. Azt javasoljuk, hogy minden jelszót biztonságos helyen tárolja el, így jelszó alaphelyzetbe állítása, ha elfelejti, nem kell. Ha szeretné állítani a jelszót, mindenképpen azt alaphelyzetbe állítása előtt az összes felhasználó értesítése.

A Windows PowerShell felületen elérheti az eszköz soros kapcsolat használatával. Emellett azt távolról használatával a HTTP vagy HTTPS, amely további biztonságot nyújtanak. HTTPS vagy egy soros, vagy a HTTP-kapcsolat-nál magasabb fokú biztonságot nyújt. Azonban HTTPS protokoll használatához először telepítenie kell egy tanúsítványt az ügyfélszámítógépen, amely hozzá fog férni az eszközhöz. A távelérés tanúsítványt tölthet le az eszköz konfigurációs lapján, a StorSimple-Eszközkezelő szolgáltatásban. Ha a tanúsítvány a távoli hozzáféréshez elveszett, le kell töltenie egy új tanúsítványt és azt, hogy a távoli felügyeleti használatára jogosult összes ügyfél való propagálása.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Ellenőrző kérdés Handshake Authentication Protocol (CHAP) kezdeményező és a célként megadott jelszavak

A CHAP protokoll egy távoli ügyfelek identitásának ellenőrzése a StorSimple-eszköz által használt hitelesítési sémával. Az ellenőrzés megosztott jelszó alapján történik. Lehet, hogy a CHAP egyirányú (egyirányú) vagy a kölcsönös (kétirányú). Az egyirányú CHAP a cél (a StorSimple-eszköz) hitelesíti egy kezdeményező (fogadó). Kölcsönös vagy fordított CHAP szükséges, hogy a cél a kezdeményező hitelesítéséhez és a kezdeményező hitelesítse a cél. A StorSimple beállítható úgy, hogy bármelyik módszerrel.

Vegye figyelembe a következőket, ha a CHAP konfigurálása:

* A CHAP-felhasználónév 233-nél kevesebb karaktert tartalmazhat.
* A CHAP-jelszó 12 és 16 karakter között kell lennie. Próbál hosszabb felhasználónév vagy jelszó a Windows-állomás hitelesítési hibát eredményez.
* A CHAP-kezdeményező és CHAP-cél nem használhatja ugyanazt a jelszót.
* A jelszó beállítása után is módosítható, de nem lehet beolvasni. Ha a jelszó módosítása, mindenképpen az összes távoli hozzáférés a felhasználók értesítése, hogy azok sikeresen csatlakozott-e a StorSimple-eszköz.

A CHAP és a StorSimple megoldás konfigurálásának kapcsolatos további információkért látogasson el [CHAP konfigurálása a StorSimple-eszköz](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager jelszava

A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modulja által használt kötet csoportok és a Windows kötet árnyékmásolata szolgáltatás alkalmazásfüggő biztonsági másolatok létrehozásához. Emellett használhatja a StorSimple Snapshot Manager biztonsági mentési ütemezés és a klón létrehozásához, vagy kötetek visszaállítása.

Konfigurál egy eszközt a StorSimple Snapshot Manager használata, ha szükséges, adja meg a StorSimple Snapshot Manager jelszavát. Ez a jelszó először be a Windows PowerShell storsimple-höz készült regisztrációja során. A jelszó is állítsa be, és a StorSimple-Eszközkezelő szolgáltatás megváltozott. Ez a jelszó hitelesíti az eszközt a StorSimple Snapshot Managerrel.

![StorSimple Snapshot Manager jelszava](./media/storsimple-security/SnapshotMgrPassword.png)

A StorSimple Snapshot Manager jelszavát 14 – 15 karakter lehet, és 3 vagy több nagybetűket, kisbetűket, számjegyeket és speciális karakterek kombinációjából kell tartalmaznia. A StorSimple Snapshot Manager jelszavának beállítása után is módosítható, de nem lehet beolvasni. Ha megváltoztatja a jelszót, ügyeljen arra, értesítse az összes távoli felhasználót.

A StorSimple Snapshot Managerrel kapcsolatos további információkért lépjen [Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Ajánlott eljárások a jelszavakhoz

Javasoljuk, hogy érdekében győződjön meg arról, hogy a StorSimple-jelszavak erős és a magas szintű védelmet használja az alábbi irányelveket:

* A jelszavak háromhavonta módosítása A jelszavak módosítása a évente kényszerítve.
* Használjon erős jelszavakat. További információért ugorjon [erősebb jelszót létrehozni, és azok védelme](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Mindig használjon különböző jelszót eltérő hozzáférési mechanizmusok; a megadott jelszavak mindegyike egyedinek kell lennie.
* Ne ossza meg jelszót bárki, aki nem jogosult a StorSimple eszköz elérésére.
* Ne előadásokat elé mások jelszó és jelszó formátumát a mutatót.
* Ha azt gyanítja, hogy egy fiók vagy a jelszó biztonsága sérült, az információk biztonsági részleg jelentéseket az incidens.
* Minden jelszót gyökérkönyvtárral bizalmas, bizalmas információkat. 

## <a name="storsimple-data-protection"></a>A StorSimple-adatok védelme

Ez a szakasz ismerteti a StorSimple biztonsági funkciók, amelyek az átvitt adatokat és a tárolt adatok védelméhez.

Ahogy további fejezeteiben bővebben ismertetjük, jelszavak felhasználók engedélyezése vagy hitelesítése előtt, hogy hozzáférhessenek a StorSimple megoldáshoz szolgálnak. Egy másik biztonsági szempont védi az adatokat a jogosulatlan felhasználók átvitel közben, akkor alatt tárolási rendszerek között, és amíg folyamatban van tárolva. A következő szakaszok ismertetik a StorSimple a megadott adatvédelmi funkciókat.

> [!NOTE]
> A deduplikáció a StorSimple eszközön, és a Microsoft Azure storage-ban tárolt adatok további védelmet biztosít. Ha a deduplikált adatok, az adatobjektumok tárolódnak a metaadatokból leképezéséhez és érheti el őket: nincs elérhető tárolási szintű környezet helyreállítani az adatokat, a kötetek szerkezete, a fájlrendszert vagy a fájl nevét.


## <a name="protect-data-flowing-through-the-service"></a>A szolgáltatás áthaladó adatok védelme

A StorSimple-Eszközkezelő szolgáltatás elsődleges célja, hogy kezelheti és konfigurálhatja a StorSimple-eszköz. A StorSimple-Eszközkezelő szolgáltatás fut a Microsoft Azure-ban. Az Azure portal használatával adja meg az eszköz konfigurációs adatokat, és ezután a Microsoft Azure használatával a StorSimple-Eszközkezelő szolgáltatás adatokat küld az eszköznek. A StorSimple a rendszer, aszimmetrikus kulcspárokat használatával biztosíthatja, hogy az Azure-szolgáltatás biztonságának sérülése nem eredményez tárolt információ-biztonság sérülését.

![Továbbított adatok titkosítása](./media/storsimple-security/DataEncryption.png)

Az aszimmetrikus kulcs rendszer védi az adatokat, amelyek a szolgáltatás a következő végig:

1. Adatok titkosítási tanúsítvány, amely egy aszimmetrikus nyilvános és titkos kulcs pár jön létre az eszközön, valamint az adatok védelmére szolgál. A kulcsok akkor jönnek létre, amikor az első eszköz regisztrálva van.
2. Az adatok titkosítási tanúsítvány kulcsait a szolgáltatásadat-titkosítási kulcs, amely által az első eszköz regisztrációja során véletlenszerűen generált erős 128 bites kulcs által védett fájllá személyes információcsere (.pfx) exportálása.
3. A tanúsítvány nyilvános kulcsa biztonságosan elérhető a StorSimple-Eszközkezelő szolgáltatásban, és a titkos kulcs az eszközön maradnak.
4. A szolgáltatás adat titkosítása a nyilvános kulcs és visszafejtése a titkos kulcs az eszközön tárolt annak biztosítása, hogy az Azure-szolgáltatás nem tudja visszafejteni a adatforgalmát az eszközön.

A szolgáltatásadat-titkosítási kulcs jön létre a csak az első eszköz regisztrálva a szolgáltatásban. A szolgáltatásban regisztrált összes ezt követő eszközök kell használnia az azonos szolgáltatásadat-titkosítási kulcs.

> [!IMPORTANT]
> Nagyon fontos másolatot készít a szolgáltatásadat-titkosítási kulcsot, és mentse azt egy biztonságos helyre. A szolgáltatásadat-titkosítási kulcs biztonsági másolatának oly módon, hogy egy arra jogosult személy is elérhető lesz, és az eszköz-rendszergazdai egyszerűen továbbítani kell tárolni.
> 
> A szolgáltatásadat-titkosítási kulcs elveszne, ha a Microsoft támogatási szolgálatnak segítségével is lekérheti azt, feltéve, hogy legalább egy eszköz online állapotban van. Azt javasoljuk, hogy a szolgáltatásadat-titkosítási kulcs módosítása után lekéri azt.

A szolgáltatásadat-titkosítási kulcs és a megfelelő adatok titkosítási tanúsítvány megváltoztatásához kövesse [módosítani a szolgáltatásadat-titkosítási kulcsot a StorSimple-Eszközkezelő szolgáltatás](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). A titkosítási kulcsok megváltoztatása megköveteli, hogy minden eszköz frissíteni kell-e az új kulccsal. Ezért azt javasoljuk, módosíthatja a kulcsot, ha az összes eszköz online állapotban. Ha az eszköz offline állapotban, a kulcsok egy másik időpontra is módosítható. Az elavult kulccsal rendelkező eszközök továbbra is képesek készüljön biztonsági mentés lesznek, de nem lesz a kulcs frissül, amíg az adatok helyreállítását.

A szolgáltatásadat-titkosítási kulcsot, és az adatok titkosítási tanúsítvány nem jár le. Azt javasoljuk azonban, hogy módosította-e a szolgáltatásadat-titkosítási kulcs évente, hogy megakadályozza a kulcs biztonsága sérült.

## <a name="protect-data-at-rest"></a>Inaktív adatok védelme

A StorSimple-eszköz helyileg és a felhőben, attól függően, a használat gyakorisága, tárolja őket szinten kezeli az adatokat. Az összes olyan gazdagépeken, amelyek az eszköz csatlakozik az eszköz, amely adatokat helyez át a felhőbe, szükség szerint történő adatküldéshez. Az átvitt adatok az eszközről a felhőbe biztonságos az interneten keresztül. Minden eszközhöz tartozik egy iSCSI-tároló, amely elérhetővé teszi az összes megosztott kötetek ezen az eszközön. Összes adat titkosítva van a felhőbeli tárhelyén való továbbítás előtt. 

![felhőalapú tárolás titkosítási kulcsa](./media/storsimple-security/CloudStorageEncryption.png)

Biztonsági és a felhőben áthelyezett adatok integritásának biztosítása érdekében a StorSimple lehetővé teszi felhőalapú tárolás titkosítási kulcsok megadása a következő:

* A felhőalapú tárolás titkosítási kulcsát adja meg, amikor hoz létre egy kötettárolót. A kulcs nem módosítható, és később hozzáadott.
* A kötettároló összes kötet ugyanazt a titkosítási kulcsot használják. Ha azt szeretné, hogy a titkosítási egy meghatározott kötete esetében egy másik formája, azt javasoljuk, hogy hoz létre egy új kötettároló kötetet üzemeltetéséhez.
* Amikor a felhőalapú tárolás titkosítási kulcsát a StorSimple-Eszközkezelő szolgáltatásban, a kulcs titkosítva van, a nyilvános részét a szolgáltatásadat-titkosítási kulcsot használja, és elküldi az eszköznek.
* A felhőalapú tárolás titkosítási kulcsa nem tárolódik a szolgáltatásban, és csak az eszköz számára ismert.
* A felhőalapú tárolás titkosítási kulcsa megadása nem kötelező. Az eszköz a gazdagépet a titkosított adatokat küldhet.

### <a name="additional-security-best-practices"></a>További ajánlott biztonsági eljárások

* Felosztott forgalom: különítse el az iSCSI SAN érkező felhasználói forgalom egy vállalati hálózaton, és a egy teljesen elkülönített hálózatot és VLAN-OK használata, ahol fizikai elszigetelési lehetőség nem. Egy iSCSI-tároló esetén dedikált hálózati garantálja a biztonság és az üzleti szempontból kritikus fontosságú adatok teljesítményét. Tárolás és a felhasználói forgalom keverése vállalati helyi hálózaton keresztül nem javasolt és is növelheti a késést és hálózati hibákat okozhat.
* A gazdagép-oldali hálózati biztonság érdekében használja a TCP/IP-tehermentesítés motor (TOE) támogató hálózati adaptereket. TOE CPU-terhelés csökkenti a hálózati adapteren TCP feldolgozása.

## <a name="protect-data-via-storage-accounts"></a>Storage-fiókok keresztül adatok védelme

Minden Microsoft Azure-előfizetés egy vagy több tárfiókot is létrehozhat. (A storage-fiók egy egyedi névteret biztosít az Azure-felhőben tárolt adatokkal folytatott munkához.) A tárfiókhoz való hozzáférést a storage-fiókhoz társított az előfizetés és a hozzáférési kulcsok vezérlik majd.

Amikor létrehoz egy tárfiókot, a Microsoft Azure két 512 bites tárelérési kulcsot, amelyek közül az egyik rendszer használja a hitelesítéshez, ha a StorSimple-eszköz hozzáfér a tárfiókhoz állít elő. Vegye figyelembe, hogy ezek a kulcsok csak az egyik használatban van. A másik hívóbetűt tárolt tartalék, amely lehetővé teszi a kulcsok rotálására rendszeres időközönként. Kulcsok rotálására, a másodlagos kulcsot aktívvá, és törölje az elsődleges kulcsot. Ezután létrehozhat egy új kulcsot használatra során a következő rotálásra. (Biztonsági okokból az adatközpontok számos szükséges kulcsrotálás.)

Javasoljuk, hogy kövesse az ajánlott eljárások kulcsrotálás:

* Tárfiókkulcsok rendszeresen annak érdekében, hogy a tárfiók nem érhető el a jogosulatlan felhasználók kell elforgatása.
* Rendszeres időközönként az Azure-rendszergazda kell módosítani, vagy az elsődleges vagy másodlagos kulcsának újragenerálása: közvetlenül hozzáférni a tárfiókhoz a Storage szakaszban az Azure Portal használatával.

## <a name="protect-data-via-encryption"></a>Titkosítási adatok védelme

A StorSimple-ban tárolt adatok védelme érdekében a következő titkosítási algoritmusokat vagy utazás a StorSimple megoldás összetevői közötti használ.

| Algoritmus | Kulcshossz | Protokollok/applications/megjegyzések |
| --- | --- | --- |
| RSA |2048 |RSA 1-PKCS 1.5-ös verzióját használják az Azure Portalon az eszközre elküldött konfigurációs adatok titkosítása: például tárolási fiók hitelesítő adatait, a StorSimple eszköz konfigurációs, és a felhőalapú tárolás titkosítási kulcsokat. |
| AES |256 |AES-CBC segítségével titkosíthatja a szolgáltatásadat-titkosítási kulcs nyilvános részét, a StorSimple-eszközről az Azure Portalra való továbbítás előtt. Azt is használják a StorSimple-eszköz adatok titkosítása a felhős társzolgáltatás fiókjába az adatok elküldése előtt. |

## <a name="storsimple-cloud-appliance-security"></a>A StorSimple Cloud Appliance biztonsági

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Személyes adatok kezelése

A fizikai és virtuális Series StorSimple-Eszközkezelő gyűjt személyes adatokat a következő kulcs példányok:

- Riasztás, ahol a felhasználók e-mail-címe konfigurálva vannak-e a felhasználói beállításokat. Ez az információ is megtekinthető és nincs bejelölve, a rendszergazda által. Ez a StorSimple 8000 sorozatú eszközök és a StorSimple Virtual Arrayt vonatkozik.
  * Megtekintheti, és törölje a beállításokat a StorSimple 8000 sorozat, kövesse a [megtekintése és kezelése a StorSimple-riasztások](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Megtekintheti, és törölje a beállításokat a StorSimple Virtual Array, kövesse a [megtekintése és kezelése a StorSimple-riasztások](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Felhasználók, akik férhet hozzá az adatokhoz, a megosztások elhelyezkedhet. Felhasználók férhetnek hozzá a megosztás adataihoz listája jelenik meg, és tekinthetnek meg. Ez a lista is törlődik. a megosztások törlésekor. Ez csak StorSimple Virtual Arrayt vonatkozik.
  * Felhasználó, aki férhetnek hozzá, vagy törli a megosztást, kövesse a listájának megtekintéséhez [kezelése a StorSimple Virtual Array-megosztások](storsimple-virtual-array-manage-shares.md)

További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Az alábbi táblázat néhány kapcsolatos kérdéseket és válaszokat biztonsági és a Microsoft Azure storsimple szolgáltatásról.

**KÉRDÉS:** A szolgáltatás biztonsága sérül. Milyen kell összeállításának következő lépései?

**VÁLASZ:** Célszerű azonnal módosítani a szolgáltatásadat-titkosítási kulcs és a tárfiók kulcsait a rétegzési adatok használt tárfiók. Útmutatás Ugrás:

* [A szolgáltatásadat-titkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [A tárfiókok kulcsrotálás](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**KÉRDÉS:** Van egy új StorSimple-eszköz, amely a szolgáltatás regisztrációs kulcsának kért. Hogyan azt beolvasása?

**VÁLASZ:** Ezt a kulcsot a StorSimple-Eszközkezelő szolgáltatás első létrehozásakor lett létrehozva. A StorSimple-Eszközkezelő szolgáltatás használatával csatlakozni az eszközhöz, ha a szolgáltatás gyors üzembe helyezési oldal segítségével megtekintéséhez vagy a szolgáltatás regisztrációs kulcsának újragenerálása. Új szolgáltatás regisztrációs kulcsának generálása nem érinti a már regisztrált eszközök. Útmutatás Ugrás:

* [Megtekintheti, vagy a szolgáltatás regisztrációs kulcsának újragenerálása](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**KÉRDÉS:** A szolgáltatásadat-titkosítási kulcs elvész. Mit tegyek?

**VÁLASZ:** Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával. Akkor is jelentkezzen be egy támogatási munkamenetet az eszközön, és segítséget lekérni a kulcsot (feltéve, hogy legalább egy eszköz online állapotban). A szolgáltatásadat-titkosítási kulcs beszerzéséhez után azonnal akkor kell megváltoztatnia, annak érdekében, hogy az új kulccsal csak Ön számára ismert. Útmutatás Ugrás:

* [A szolgáltatásadat-titkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**KÉRDÉS:**  Tudom a jogosult a szolgáltatási adatok titkosítási kulcs változásait egy eszközt, de a kulcsváltozás folyamat nem indult el. Mit tegyek?

**VÁLASZ:** Ha az időkorlát lejárt, szüksége lesz a engedélyezze újra az eszközt a szolgáltatási adatok titkosítási kulcs módosítás, és indítsa újra a folyamatot.

**KÉRDÉS:**  A szolgáltatásadat-titkosítási kulcs módosítása, de a többi eszköz frissítése 4 órán belül nem tudta I. Kell újra elindítani?

**VÁLASZ:** A 4 órás időszakon belül van csak a kezdeményezése a módosítást. A jogosult StorSimple eszközön a frissítési folyamat megkezdése után az engedélyezési akkor érvényes, addig, amíg az összes eszköz frissülnek.

**KÉRDÉS:** A StorSimple-rendszergazdától elhagyta a vállalatot. Mit tegyek?

**VÁLASZ:** Módosítsa a jelszót, amely a StorSimple-eszközhöz való hozzáférés engedélyezése, és módosítani a szolgáltatásadat-titkosítási kulcs győződjön meg arról, hogy az új információk jogosulatlan személyek nem ismert. Útmutatás Ugrás:

* [A StorSimple-Eszközkezelő szolgáltatás használata a storsimple-jelszavak módosítása](storsimple-8000-change-passwords.md)
* [A szolgáltatásadat-titkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [A CHAP konfigurálása a StorSimple eszköz](storsimple-8000-configure-chap.md)

**KÉRDÉS:** Szeretnék megadni a StorSimple Snapshot Manager jelszavát egy gazdagépre, amely a StorSimple-eszköz csatlakozik, de a jelszó nem érhető el. Mi a teendő?

**VÁLASZ:** Ha elfelejtette a jelszavát, akkor hozzon létre egy újat. Ezt követően feltétlenül összes meglévő felhasználót tájékoztatja, hogy a jelszó megváltozott, és, hogy azok frissítenie kell az ügyfelek az új jelszóval. Útmutatás Ugrás:

* [A StorSimple Snapshot Manager jelszavának módosítása](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Egy eszköz hitelesítéséhez](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**KÉRDÉS:** A távoli hozzáférést a Windows PowerShell StorSimple-tanúsítvány megváltozott az eszközön. Hogyan frissíthetem a távelérési ügyfelek?

**VÁLASZ:** Töltse le az új tanúsítványt a StorSimple-Eszközkezelő szolgáltatás, és adja meg azt a távoli ügyfelek tanúsítványtárolójában kell telepíteni. Útmutatás Ugrás:

* [Tanúsítvány importálása a parancsmag](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**KÉRDÉS:** A védett adatok if a StorSimple-Eszközkezelő szolgáltatás biztonsága sérül van?

**VÁLASZ:** Egy webböngészőben megtekintheti szolgáltatás konfigurációs adatok mindig titkosítva a nyilvános kulcsot. A szolgáltatás nem rendelkezik hozzáféréssel a titkos kulcsot, mert a szolgáltatás nem lesz képes jelennek meg adatok. A StorSimple-Eszközkezelő szolgáltatás biztonsága sérül, van-e nem érinti, mivel nincsenek a StorSimple-Eszközkezelő szolgáltatásban tárolt kulcsok.

**KÉRDÉS:** Ha valaki hozzáfér a adatok titkosítási tanúsítványt, lesz az adatok biztonsága sérülhet?

**VÁLASZ:** A Microsoft Azure az ügyfél szolgáltatásadat-titkosítási kulcsot (.pfx-fájl) titkosított formában tárolja. Mivel a .pfx fájl titkosítva van, és a StorSimple-szolgáltatás nem rendelkezik a szolgáltatásadat-titkosítási kulcs visszafejtése a .pfx-fájlt, egyszerűen hozzáférhetne a .pfx-fájlba fogja nem teszik elérhetővé a titkos kulcsok.

**KÉRDÉS:** Mi történik, ha egy intézkedése kéri a Microsofttól az adataim?

**VÁLASZ:** Összes adat titkosítva van a szolgáltatásban, és a titkos kulcs az eszközhöz marad, mert a intézkedése kérje meg az ügyfél az adatok.



## <a name="next-steps"></a>További lépések

[A StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

