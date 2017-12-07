---
title: "A StorSimple 8000 series biztonsági |} Microsoft Docs"
description: "A StorSimple szolgáltatás, eszköz, és az adatok védelme a helyszíni és felhőalapú biztonsági és adatvédelmi szolgáltatásait ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/06/2017
ms.author: alkohli
ms.openlocfilehash: a8990d68b327e5688c7078a6b1a9d41ad0600a67
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple-biztonság és adatvédelem

## <a name="overview"></a>Áttekintés

A biztonság az fő szempont bárki, aki bevezetni új technológia, különösen akkor, amikor a technológia bizalmas vagy szellemi tulajdont képező adatokat használják. Szerint értékeli a különböző technológiákkal, át kell gondolnia, nagyobb kockázattal és a data protection költségeket. Microsoft Azure StorSimple nyújt egy biztonsági és adatvédelmi megoldás a data protection segítséget nyújt annak biztosításához:

* **Bizalmas** – csak az arra jogosult személyek is megtekintheti az adatokat.
* **Integritás** – csak a jogosult személyek módosíthatja vagy törölheti az adatokat.

A Microsoft Azure StorSimple megoldáshoz kapcsolatba egymással négy fő összetevőből áll:

* **A Microsoft Azure StorSimple Device Manager szolgáltatott** – a felügyeleti szolgáltatás, amellyel konfigurálhatja, és helyezze üzembe a StorSimple eszköz.
* **A StorSimple eszköz** – az adatközpontban telepített fizikai eszköz. Összes gazdagép és az ügyfelek, amely adatokat generál a StorSimple eszköz csatlakozhat, és az eszköz kezeli az adatokat, és áthelyezi az Azure felhőalapú szükség szerint.
* **Az eszközhöz csatlakoztatott ügyfelek/állomások** – az ügyfelek a infrastruktúrában, a StorSimple eszköz csatlakozhat, és hozhat létre, amelyet a védeni kívánt adatokat.
* **Felhőbeli tárhely** – a hely adatokat tároló Azure felhőben.

A következő szakaszok ismertetik a StorSimple biztonsági funkciók ezeket az összetevőket, és a rajtuk tárolt adatok védelme érdekében. Azt is, lehetséges, hogy a Microsoft Azure StorSimple biztonsági és a hozzájuk tartozó válaszok kapcsolatos kérdések listáját.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple Device Manager szolgáltatás védelme

A StorSimple Device Manager szolgáltatás nem a Microsoft Azure-ban üzemeltetett, és minden StorSimple eszköz, amely a szervezet közvetített kezelésére szolgáló felügyeleti szolgáltatást. A StorSimple Device Manager szolgáltatás által a szervezeti hitelesítő adataival bejelentkezni az Azure-portálon webböngészőn keresztül végezheti el.

A StorSimple Device Manager szolgáltatás használatához, hogy a szervezet rendelkezik-e az Azure-előfizetéssel, amely tartalmazza a StorSimple. Az előfizetés az Azure-portálon keresztül elérhető funkciók szabályozza. Ha a szervezet nem rendelkezik Azure-előfizetés és a velük kapcsolatos tudnivalókért lásd: szeretné [regisztráció az Azure-bA szervezetként](../active-directory/sign-up-organization.md).

Mivel a StorSimple Device Manager szolgáltatás az Azure-ban üzemel védi az Azure biztonsági funkciók. A Microsoft Azure által biztosított biztonsági funkciókkal kapcsolatos további információkért látogasson el a [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>A StorSimple eszköz védelme

A StorSimple eszköz azonban egy helyszíni hibrid tárolóeszköz, amely tartalmazza (SSD) és a merevlemezes (HDD) meghajtók, redundáns vezérlők és az automatikus feladatátvételi lehetőségeket. A tartományvezérlők kezelése a tároló rétegezésével, elhelyezéséhez jelenleg használt (vagy kiemelt) adatok helyi tárolóban (a StorSimple eszköz vagy a helyszíni kiszolgálók), miközben ritkábban használt adatok áthelyezését a felhőbe.

Kizárólag engedélyezett eszközök számára engedélyezett a StorSimple Device Manager szolgáltatásban, az Azure-előfizetéshez létrehozott StorSimple. Egy eszköz hitelesítése, regisztrálnia kell azt a StorSimple eszköz Manager szolgáltatásban Szolgáltatásregisztrációs kulcs megadásával. A szolgáltatás regisztrációs kulcsának az Azure-portálon létrehozott 128 bites véletlenszerű kulcsot.

![Szolgáltatásregisztrációs kulcs](./media/storsimple-security/ServiceRegistrationKey.png)

Megtudhatja, hogyan be a szolgáltatás regisztrációs kulcsának, keresse fel [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

A szolgáltatás regisztrációs kulcsának, mint egy hosszú kulcs 100 + karaktereket tartalmaz. Másolja a vágólapra a kulcsot, és mentse egy biztonságos helyre a fájlt, hogy szükség esetén további eszközök engedélyezésére használja. Ha a szolgáltatás regisztrációs kulcsának elveszik, az első eszköz regisztrálása után, létrehozhat egy új kulcsot a StorSimple Device Manager szolgáltatásból. Ez nem érinti a meglévő eszközök működését.

Eszköz regisztrálása után jogkivonatokat használ a Microsoft Azure folytatott kommunikációhoz. A szolgáltatás regisztrációs kulcsának nem használatos eszköz regisztrálása után.

> [!NOTE]
> Azt javasoljuk, hogy a szolgáltatás regisztrációs kulcsának követően minden generálja újra.


## <a name="protect-your-storsimple-solution-via-passwords"></a>A StorSimple megoldás a jelszavak védelme

Jelszavak segítségével nagymértékben a StorSimple megoldásban győződjön meg arról, hogy az adatok csak az engedéllyel rendelkező felhasználók számára elérhető, és a számítógép biztonsági fontos eleme. StorSimple lehetővé teszi a következő jelszavak beállítása:

* A StorSimple eszköz rendszergazdai jelszava
* Ellenőrző kérdés kezdeményező és a cél jelszavak Handshake Authentication Protocol (CHAP)
* StorSimple Snapshot Manager jelszava

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell a StorSimple és a StorSimple eszköz rendszergazdai jelszava

A Windows PowerShell-lel egy parancssori felületet a StorSimple eszköz kezelésére használható. A Windows PowerShell-lel rendelkezik funkciókat, amelyek lehetővé teszik az eszköz regisztrálása, állítsa be a hálózati illesztő az eszközön, bizonyos típusú frissítések telepítése, az eszköz hibaelhárításához a támogatási munkamenet elérésével, és módosítsa az eszköz állapotát. A StorSimple Windows PowerShell az eszköz soros konzoljához való kapcsolódás vagy a Windows PowerShell távoli eljáráshívás segítségével érheti el.

PowerShell távvezérlése HTTPS vagy HTTP keresztül végezhető. Ha engedélyezve van a Rendszerfelügyeleti webszolgáltatások HTTPS protokollon, szüksége lesz a távfelügyeleti tanúsítvány letöltéséhez az eszközről, és telepítse azt a távoli ügyfél. PowerShell távvezérlése kapcsolatos további információkért látogasson el [távolról csatlakozhat a StorSimple eszköz](storsimple-8000-remote-connect.md).

Miután az eszköz csatlakozni a Windows PowerShell-lel, szüksége lesz arra, hogy az eszköz rendszergazdai jelszava bejelentkezni az eszközt.

![Az eszköz rendszergazdai jelszava](./media/storsimple-security/DeviceAdminPW.png)

Vegye figyelembe az alábbi gyakorlati tanácsokat:

* Távfelügyelet alapértelmezés szerint ki van kapcsolva. A StorSimple Device Manager szolgáltatás segítségével engedélyezheti azt. Biztonsági szempontból ajánlott távelérés csak időszakban a ténylegesen igényelt engedélyezni kell.
* Ha megváltoztatja a jelszavát, ügyeljen arra, hogy minden távelérési a felhasználók értesítése, így azok nem tapasztalnak egy váratlan kapcsolatok megszakadását.
* A StorSimple Device Manager szolgáltatás nem tudja beolvasni a meglévő jelszavak: Ez csak alaphelyzetbe állíthatja őket. Azt javasoljuk, jelszavak biztonságos helyen tárolja el, így nem kell állítani a jelszót, ha elfelejti azt. Ha szeretné állítani a jelszót, ügyeljen arra, hogy értesítse az összes felhasználót ahhoz, hogy állítsa alaphelyzetbe.

Az eszköz soros kapcsolat használatával végezheti el a Windows PowerShell-felületet. Emellett azt távolról HTTP vagy HTTPS-t, amely további biztonsági használatával. HTTPS vagy egy soros, vagy a HTTP-kapcsolat-nál magasabb szintű biztonságot nyújt. Azonban a HTTPS protokoll használatához először telepítenie kell egy tanúsítványt az ügyfélszámítógépen, az alkalmazást az eszközön. A távelérés tanúsítvány letöltheti az eszköz konfigurációs lapján, a StorSimple Device Manager szolgáltatásban. Ha táveléréshez tanúsítvánnyal elvész, akkor töltsön le egy új tanúsítvány, és terjesztése a távoli felügyeleti használatára jogosult összes ügyfélre.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Ellenőrző kérdés kezdeményező és a cél jelszavak Handshake Authentication Protocol (CHAP)

A CHAP egy hitelesítési séma érvényesítése a távoli ügyfelek identitása a StorSimple eszköz által használt protokoll. Az ellenőrzés megosztott jelszó alapul. Lehet, hogy a CHAP egyirányú (egyirányú) vagy a kölcsönös (kétirányú). Az egyirányú CHAP a cél (a StorSimple eszköz) hitelesíti a egy kezdeményező (gazda). Kölcsönös vagy fordított CHAP megköveteli, hogy a cél hitelesítéséhez a kezdeményező és a kezdeményező hitelesítse a cél. A StorSimple beállítható úgy, hogy mindkét módszert használja.

Vegye figyelembe a következőket CHAP konfigurálásakor:

* A CHAP-felhasználónév kevesebb mint 233 karaktereket tartalmazhat.
* A CHAP-jelszó 12 és 16 karakter hosszúságú lehet. Hosszabb felhasználónév vagy jelszó a Windows-állomás hitelesítési hibát eredményez.
* A CHAP-kezdeményező mind a CHAP-cél nem használhatja ugyanazt a jelszót.
* Miután beállította a jelszót, ez módosítható, de az nem olvasható. Ha a jelszót módosítják, ügyeljen arra, hogy az összes távelérési a felhasználók értesítése, hogy azok sikeresen csatlakozott-e a StorSimple eszköz.

A CHAP és a StorSimple megoldás konfigurálásának kapcsolatos további információkért látogasson el [CHAP konfigurálása a StorSimple eszköz](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager jelszava

StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modulja által használt kötet csoportok és a Windows kötet árnyékmásolata szolgáltatás alkalmazáskonzisztens biztonsági mentés létrehozásához. Emellett a StorSimple Snapshot Manager segítségével is hozzon létre biztonsági mentési ütemezés és a Klónozás vagy kötetek visszaállítása.

Ha az eszköz StorSimple Snapshot Manager használatára állítja be, meg kell arra, hogy a StorSimple Snapshot Manager jelszavát. Ez a jelszó először állítja be a Windows PowerShell StorSimple a regisztráció során. A jelszó is beállítása és a StorSimple Device Manager szolgáltatás megváltozott. Ezt a jelszót az eszköz StorSimple Snapshot Manager hitelesíti.

![StorSimple Snapshot Manager jelszava](./media/storsimple-security/SnapshotMgrPassword.png)

A StorSimple Snapshot Manager jelszava 14 – 15 karakter lehet, és 3 vagy több nagybetű, nagybetűk, numerikus és speciális karakterek kombinációjából kell tartalmaznia. A StorSimple Snapshot Manager jelszavának beállítása után ez módosítható, de az nem olvasható. Ha megváltoztatja a jelszavát, ügyeljen arra, hogy értesítse az összes távoli felhasználót.

StorSimple Snapshot Manager kapcsolatos további információkért látogasson el [Mi az StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Jelszó gyakorlati tanácsok

Azt javasoljuk, hogy érdekében győződjön meg arról, hogy a StorSimple-jelszavak erős és jól védett kövesse az alábbi iránymutatásokat:

* A jelszavak módosítása háromhavonta. A jelszavak módosítása a évente érvényesül.
* Erős jelszavakat használjon. További információkért látogasson el [erősebb jelszavak létrehozása és védelmük](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
* Mindig használjon különböző jelszót eltérő hozzáférési mechanizmusok; a megadott jelszavak mindegyikének egyedinek kell lennie.
* Ne ossza meg jelszavak bárki, aki nem jogosult a StorSimple eszközhöz való hozzáféréshez.
* Beszéd kapcsolatos elé más jelszót, vagy ne mutatót a jelszó-formátum.
* Ha azt gyanítja, hogy egy fiók vagy jelszó feltörték, jelenteni az információ-biztonság osztálynak.
* Kezelje a jelszó-és nagybetűket, bizalmas információkat. 

## <a name="storsimple-data-protection"></a>StorSimple-adatok védelme

Ez a szakasz ismerteti a StorSimple biztonsági funkciói, amely az átvitel során az adatok és a tárolt adatok védelme.

További fejezeteiben bővebben ismertetjük, jelszavak használatával kell engedélyeznie, ahhoz, hogy a StorSimple megoldásban a hozzáférést a felhasználók hitelesítésére. Egy másik biztonsági szempont védi az adatokat a jogosulatlan felhasználóktól tárolási rendszerek között, és közben az adatok tárolása alatt átvitel közben. A következő szakaszok ismertetik a StorSimple megadott adatbiztonsági funkciók.

> [!NOTE]
> A deduplikáció és a Microsoft Azure Storage a StorSimple eszközön tárolt adatok további védelmet biztosít. Amikor deduplikált adatok, az adatok objektumok hozzárendelését és a hozzáférésüket használt metaadatok alapján külön-külön tárolja: nincs elérhető tárhely szintű környezet a kötetek szerkezete, a fájlrendszer vagy a fájlnév alapján adatok helyreállítására.


## <a name="protect-data-flowing-through-the-service"></a>A szolgáltatáson keresztül áramló adatok védelme

A StorSimple Device Manager szolgáltatás elsődleges célja, kezelése és konfigurálása a StorSimple eszközt. A StorSimple Device Manager szolgáltatás fut a Microsoft Azure-ban. Az Azure-portál használatával adja meg az eszköz konfigurációs adatokat, és a Microsoft Azure segítségével a StorSimple Device Manager szolgáltatás elküldi az adatokat az eszközre. StorSimple a rendszer, aszimmetrikus kulcspárokat használatával biztosítható, hogy a biztonsági sérülését jelenti az Azure-szolgáltatás nem lesz tárolt információ-biztonság sérülését.

![Felé továbbított adatok titkosítása](./media/storsimple-security/DataEncryption.png)

Az aszimmetrikus kulcs rendszer megvédi az adatokat, amelyek az alábbiak szerint zajlik a szolgáltatáson keresztül:

1. Egy adat-titkosítási tanúsítványnak használó aszimmetrikus nyilvános és titkos kulcsok pár történik az eszközön, és az adatok védelmét. A kulcsok akkor jönnek létre, amikor az első eszköz regisztrálva van.
2. A titkosítási tanúsítvány kulcsokat a szolgáltatásadat-titkosítási kulcs, amely az első eszköz regisztrálása során véletlenszerűen létrehozott erős 128 bites kulccsal védett személyes információcsere (.pfx) fájlba exportált.
3. A tanúsítvány nyilvános kulcsát biztonságosan szeretné elérhetővé tenni a StorSimple eszköz Manager szolgáltatáshoz, és a titkos kulcs marad az eszközön.
4. Írja be a szolgáltatás adattitkosítás használata a nyilvános kulcs és visszafejtése a titkos kulcs az eszközön, győződjön meg arról, hogy az Azure-szolgáltatás nem tudja visszafejteni az eszközre áramló adatokat.

A szolgáltatásadat-titkosítási kulcs jön létre, csak az első eszköz regisztrálva a szolgáltatásban. A szolgáltatásban regisztrált összes további eszközök kell használnia az azonos szolgáltatásadat-titkosítási kulcs.

> [!IMPORTANT]
> Nagyon fontos másolatot készít a szolgáltatásadat-titkosítási kulcs, és mentse egy biztonságos helyre. A szolgáltatásadat-titkosítási kulcs biztonsági másolatának oly módon, hogy elérhetők, meghatalmazott, és az eszköz-rendszergazdai könnyen továbbítani kell tárolni.
> 
> A szolgáltatásadat-titkosítási kulcs nem vesztek el, ha a Microsoft támogatási szolgálatnak segítenek lekéréséhez, feltéve, hogy legalább egy eszköz online állapotban van. Azt javasoljuk, hogy módosítsa a szolgáltatásadat-titkosítási kulcs, miután lekéri azt. Utasításokért ugorjon [módosítása a szolgáltatásadat-titkosítási kulcs](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

A szolgáltatásadat-titkosítási kulcs és a megfelelő adatok titkosítási tanúsítványt, kövesse a lépéseket a [megváltoztatása a StorSimple Device Manager szolgáltatás a szolgáltatásadat-titkosítási kulcs](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). A titkosítási kulcsok módosításához szükséges, hogy minden eszköz frissíteni kell-e az új kulccsal. Ezért azt javasoljuk, hogy a kulcs akkor módosul, ha minden eszköz online állapotban. Kapcsolat nélküli eszközök esetén a kulcsok egy másik időpontban lehet megváltoztatni. Az elavult kulccsal rendelkező eszközöket is kíván biztonsági másolatot készíteni, de nem fogják tudni állítsa vissza az adatokat, amíg a kulcsa nem frissül.

A szolgáltatásadat-titkosítási kulcs és az adatok titkosítási tanúsítvány nem jár le. Azt javasoljuk azonban, hogy módosította-e a szolgáltatásadat-titkosítási kulcs évente, hogy megakadályozza a kulcs biztonsági sérülése.

## <a name="protect-data-at-rest"></a>Adatok inaktív védelme

A StorSimple eszköz rétegek tárolja őket helyileg, és a felhőben, attól függően, hogy a használat gyakorisága kezeli az adatokat. Az eszköz csatlakozik az összes állomás gépek adatokat továbbít az eszköz, amely majd mozgatja az adatokat a felhőbe, szükség szerint. Adatátvitel az eszközről a felhőbe biztonságosan az interneten keresztül. Minden eszközhöz tartozik egy iSCSI-tároló, amely megjeleníti az összes megosztott kötetek azokon az eszközökön. Összes adata titkosításra kerül felhőbeli tárhelyén való továbbítás előtt. 

![Felhőalapú tárolás titkosítási kulcsa](./media/storsimple-security/CloudStorageEncryption.png)

A biztonságának és integritásának a felhő át adatokat a biztosítása érdekében a StorSimple megadhatja felhőalapú tárolás titkosítási kulcsokat az alábbiak szerint:

* A kötettároló létrehozásakor megadhatja a felhőalapú tárolás titkosítási kulcsát. A kulcs nem módosítható, vagy később.
* A kötettároló összes kötet ossza meg ugyanazt a titkosítási kulcsot. Ha azt szeretné, hogy egy másik formája, amelyet egy adott kötet titkosítási, azt javasoljuk, hogy hozzon létre egy új kötettároló üzemeltetéséhez, hogy a köteten.
* Amikor a felhőalapú tárolás titkosítási kulcsát a StorSimple Device Manager szolgáltatásban, a kulcs titkosított a szolgáltatásadat-titkosítási kulcs nyilvános részének használatával, és elküldheti az eszközre.
* A felhőalapú tárolás titkosítási kulcsa nem tárolódik a szolgáltatás, amely csak az eszköz.
* A felhőalapú tárolás titkosítási kulcsát megadása nem kötelező megadni. Az eszköz a kiszolgáló titkosított adatokat küldhet.

### <a name="additional-security-best-practices"></a>Ajánlott biztonsági eljárások

* Ossza fel a forgalom: különítse el a iSCSI SAN egy vállalati helyi hálózaton felhasználói forgalomnak a telepítése egy teljesen elkülönített hálózatot és VLAN-ok használatával, ahol fizikai elkülönítési lehetőség nem érhető el. Az iSCSI-tárolóhoz egy dedikált hálózaton biztonságának és az üzleti szempontból kritikus fontosságú adatok teljesítményt garantálja. Tárolás és a felhasználói forgalom keverése egy vállalati helyi hálózaton keresztül nem ajánlott és is növelheti a késés és hálózati hibákhoz vezethet.
* Gazdagép-oldali hálózati biztonság érdekében a TCP/IP-kiszervezés motor (TOE) támogató hálózati felületek használatával. TOE CPU-terhelést, csökkenti a hálózati adapteren TCP feldolgozása.

## <a name="protect-data-via-storage-accounts"></a>Storage-fiókok keresztül az adatok védelme

Minden Microsoft Azure-előfizetés egy vagy több storage-fiókokat hozhat létre. (A storage-fiók egy egyedi névteret biztosít az Azure felhőben tárolt adatai.) A tárfiók eléréséhez az előfizetés és a hozzáférési kulcs tárolási fiókhoz társított vezérli.

Amikor létrehoz egy tárfiókot, a Microsoft Azure két 512 bites tárelérési kulcsot, amelyek közül az egyik használatos a hitelesítéshez a StorSimple eszköz fér hozzá a tárfiók állít elő. Vegye figyelembe, hogy ezek a kulcsok csak az egyik használatban van. A más kulcs-tartalék értékét, hogy lehetővé teszi a kulcsok rendszeresen elforgatása használatban van. Kulcsok megváltoztatása, a másodlagos kulcs aktiválásához, és törölje a az elsődleges kulcs. Ezután létrehozhat új kulcs a következő Elforgatás során. (Biztonsági okokból sok adatközpontokban van szükség kulcs elforgatás.)

Azt javasoljuk, hogy kövesse az alábbi gyakorlati tanácsok a kulcs elforgatás:

* Tárfiókkulcsok rendszeresen segítségével győződjön meg arról, hogy a tárfiók jogosulatlan felhasználók nem férhetnek hozzá kell elforgatása.
* Rendszeres időközönként az Azure rendszergazdai kell módosítani, vagy újragenerálja az elsődleges vagy másodlagos kulcsot a tároló szakaszban az Azure portál segítségével közvetlenül a tárfiók eléréséhez.

## <a name="protect-data-via-encryption"></a>Titkosítási keresztül az adatok védelme

StorSimple használja a következő titkosítási algoritmusok tárolt adatok védelmére vagy a StorSimple megoldásban összetevői közötti utazás.

| Algoritmus | Kulcshossz | Protokollok/applications/megjegyzések |
| --- | --- | --- |
| RSA |2048 |RSA 1-PKCS 1.5-ös verzióját használják az Azure-portálon az eszközre küldött konfigurációs adatok titkosítása: például a tárolási fiók hitelesítő adatait, a StorSimple eszköz konfigurációs, és a felhőalapú tárolás titkosítási kulcsokat. |
| AES |256 |A szolgáltatásadat-titkosítási kulcs nyilvános részének titkosítását az Azure portálra a StorSimple-eszközről küldött CBC az AES szolgál. Azt is használják a StorSimple eszköz adattitkosítás az adatok a felhőalapú társzolgáltatás fiókja történő elküldése előtt. |

## <a name="storsimple-cloud-appliance-security"></a>StorSimple felhő készülék biztonsági

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Az alábbi táblázat néhány kapcsolatos kérdések és válaszok biztonsági és a Microsoft Azure StorSimple.

**K:** a szolgáltatás biztonsága sérül. Milyen kell összeállításának következő lépései?

**V:** a szolgáltatásadat-titkosítási kulcs és a tárfiók kulcsait a tárfiók rétegezési adatok használt azonnal meg kell változtatni. Útmutatásért tekintse:

* [A szolgáltatásadat-titkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Kulcs elforgatási szögét storage-fiókok](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**K:** , amely a szolgáltatás regisztrációs kulcsának kért új StorSimple eszköz. Hogyan azt lekérése?

**V:** ezt a kulcsot jött létre, amikor a StorSimple Device Manager szolgáltatás létrehozásakor. A StorSimple Device Manager szolgáltatás használatával csatlakozzon az eszközhöz, használhatja a szolgáltatás gyors üzembe helyezési oldal megtekintéséhez, vagy a szolgáltatás regisztrációs kulcsának újragenerálása. Egy új szolgáltatás regisztrációs kulcs nem érinti a meglévő regisztrált eszközöket. Útmutatásért tekintse:

* [Megtekintéséhez vagy a szolgáltatás regisztrációs kulcsának újragenerálása](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**K:** a szolgáltatásadat-titkosítási kulcs elvész. Mit tegyek?

**V:** forduljon a Microsoft támogatási szolgálatához. Ezek jelentkezhetnek be az eszközön és a kulcs lekérését (feltéve, hogy legalább egy eszköz online állapotban) Súgó támogatása munkamenethez. A szolgáltatásadat-titkosítási kulcs beszerzése, után azonnal akkor kell megváltoztatnia annak érdekében, hogy az új kulcs csak az Ön is ismert. Útmutatásért tekintse:

* [A szolgáltatásadat-titkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**K:** szeretnék a szolgáltatás titkosítási kulcs változását eszköz engedélyezett, de nem indult el a fő folyamatot. Mit tegyek?

**V:** Ha lejárt az időkorlát, akkor ismét engedélyezheti az eszköz számára a szolgáltatás titkosítási kulcs változását, és indítsa újra a folyamatot.

**K:** módosítottam a szolgáltatásadat-titkosítási kulcs, de nem sikerült frissíteni az egyéb eszközöket 4 órán belül. Indítsa el újból a kell?

**V:** 4 órás időszak az az csak a kezdeményező a módosítás. Után a frissítési folyamat elindításához az arra jogosult StorSimple eszközön, akkor az engedélyezési nem érvényes, amíg minden eszköz frissítése.

**K:** a StorSimple rendszergazda kilépett a vállalattól. Mit tegyek?

**V:** változás- és a jelszavakat, amely engedélyezi a hozzáférést a StorSimple eszközhöz, és módosítsa a szolgáltatásadat-titkosítási kulcs annak érdekében, hogy az új adatok nem ismert, hogy alaphelyzetbe állítása nem engedélyezett a csoporthoz. Útmutatásért tekintse:

* [A StorSimple Device Manager szolgáltatással a storsimple-jelszavak módosítása](storsimple-8000-change-passwords.md)
* [A szolgáltatásadat-titkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [A CHAP konfigurálása a StorSimple eszköz](storsimple-8000-configure-chap.md)

**K:** kívánt adja meg a StorSimple Snapshot Manager jelszavát egy olyan gazdagépre, a StorSimple eszköz csatlakozik, de a jelszó nem érhető el. Mi a teendő?

**V:** Ha elfelejtette a jelszavát, akkor hozzon létre egy újat. Ezt követően lehet arra, hogy az összes meglévő felhasználók tájékoztatja, hogy a jelszó megváltozott, és, hogy azok az ügyfelek számára az új jelszót kell frissítse. Útmutatásért tekintse:

* [A StorSimple Snapshot Manager jelszavának módosítása](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Egy eszköz hitelesítéséhez](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**K:** a StorSimple a Windows PowerShell távoli hozzáférést a tanúsítvány módosítva lett, az eszközön. Hogyan frissíthetők a távelérési ügyfelek?

**V:** töltse le az új tanúsítványt a StorSimple Device Manager szolgáltatásból, és adja meg azt a tanúsítványtárolójába a távoli ügyfelek telepíteni kell. Útmutatásért tekintse:

* [Tanúsítvány importálása parancsmag](https://technet.microsoft.com/library/hh848630.aspx)

**K:** az adatok, ha a StorSimple Device Manager szolgáltatás sérült védett?

**V:** szolgáltatás konfigurációs mindig adattitkosítás a nyilvános kulccsal webböngészőben megtekintésekor. A szolgáltatás nem fér hozzá a titkos kulcsot, mert a szolgáltatás nem lesz képes jelennek meg az adatok. Ha a StorSimple Device Manager szolgáltatás biztonsága sérül, akkor ennek nincs hatása, mivel nincsenek a StorSimple Device Manager szolgáltatásban tárolt kulcsok.

**K:** Ha valaki hozzáfér az adatok titkosítási tanúsítvány, fog adataimat megsértik?

**V:** Microsoft Azure tárolja az ügyfél adattitkosítási kulcsot (.pfx-fájl), titkosított formában. Mivel a .pfx fájl titkosítva van, és a StorSimple szolgáltatás nem rendelkezik a szolgáltatásadat-titkosítási kulcs visszafejtése a .pfx fájl, egyszerűen fog hozzáférni a .pfx fájlt az nem megmutatják a titkos kulcsok.

**K:** mi történik, ha egy kormányzati entitás Microsoft kéri az adataimat?

**V:** az összes adat titkosítva van a szolgáltatásban, és a titkos kulcs megőrzi az eszközzel, mert a kormányzati entitás kérje meg az ügyfél az adatok számára.

## <a name="next-steps"></a>Következő lépések

[A StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).

