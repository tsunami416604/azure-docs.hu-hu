---
title: "Az Azure biztonsági és megfelelőségi tervezetének - FedRAMP webes alkalmazások Automation - azonosítási és hitelesítési"
description: "FedRAMP webes alkalmazások Automation - azonosítási és hitelesítési"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1033f63f-daf0-4174-a7f6-7b0f569020e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 21b5c453716f99be26c8dd6400bb3489477b4956
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
---
# <a name="identification-and-authentication-ia"></a>Azonosítási és hitelesítési (IA)

## <a name="nist-800-53-control-ia-1"></a>A NIST 800-53 vezérlő IA-1

> [!NOTE]
> Ezek az intézkedések határozzák meg NIST és az Egyesült Államok Kereskedelmi Minisztériuma a NIST különleges közlemény 800-53-as verziójának 4 részeként. Tekintse meg a NIST 800-53 fordított 4 tesztelési útmutató a vezérlők és eljárásokat olvashat.

#### <a name="identification-and-authentication-policy-and-procedures"></a>Azonosítási és hitelesítési házirend és eljárások

**IA-1** a szervezet házon belül fejlesztett alkalmazásokra, dokumentumokat, és hogy terjeszti [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] egy célú, a hatókör, a szerepkörök, a feladatkörei, a felügyeleti orvosló azonosítási és hitelesítési házirend előfizetési, koordinálásának szervezeti entitásokat, és a megfelelőségi; és eljárások végrehajtására azonosítási és hitelesítési házirend és kapcsolódó azonosítási és hitelesítési ellenőrzések; ellenőrzi, és frissíti az aktuális azonosítási és hitelesítési házirend [hozzárendelés: szervezet által meghatározott gyakoriság]; és az azonosítási és hitelesítési eljárások [hozzárendelés: szervezet által meghatározott gyakoriság].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél vállalati szintű azonosítási és hitelesítési házirendet és eljárások a vezérlő megoldására elegendő lehet. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-2"></a>A NIST 800-53 vezérlő IA-2

#### <a name="identification-and-authentication-organizational-users"></a>Azonosítási és hitelesítési (szervezeti felhasználók)

**IA-2** az információs rendszer egyedileg azonosítja, és hitelesíti a szervezeti felhasználók (vagy a szervezeti felhasználók nevében eljáró folyamatok).

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének által létrehozott fiókok jogosultak az egyedi azonosítót. Beépített fiók nem egyedi azonosítót a rendszer letiltották vagy eltávolították. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-2-1"></a>A NIST 800-53 vezérlő IA-2 (1)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Azonosítási és hitelesítési (szervezeti felhasználók) |} Hálózati hozzáférésű fiókokhoz

**IA-2 (1)** a információk rendszer megvalósítja a többtényezős hitelesítést a hálózati hozzáférésű fiókokhoz.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A felhasználói fiókokhoz hálózati hozzáféréshez többtényezős hitelesítés megvalósítása felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-2-2"></a>A NIST 800-53 vezérlő IA-2 (2)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Azonosítási és hitelesítési (szervezeti felhasználók) |} Hálózati hozzáférés rendszerjogosultsággal nem rendelkező fiókok

**IA-2 (2)** a információk rendszer megvalósítja a többtényezős hitelesítést a hálózati hozzáféréshez rendszerjogosultsággal nem rendelkező fiókok.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél feladata a hálózati hozzáféréshez rendszerjogosultsággal nem rendelkező fiókok többtényezős hitelesítés megvalósításához. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-2-3"></a>A NIST 800-53 vezérlő IA-2 (3)

#### <a name="identification-and-authentication-organizational-users--local-access-to-privileged-accounts"></a>Azonosítási és hitelesítési (szervezeti felhasználók) |} Helyi hozzáférésű fiókokhoz

**IA-2 (3)** a információk rendszer megvalósítja a többtényezős hitelesítést a helyi hozzáférésű fiókokhoz.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Ügyfél nem rendelkeznek helyi hozzáférni semmilyen rendszer erőforráshoz az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure nem engedélyezi a helyi hozzáférési, kivéve, ha fizikai hozzáférés szükséges. Csak helyi rendszergazdai jogosultságokkal kell használni azokban az esetekben, ahol a tagkiszolgálót hálózati hibákat észlelt, és tartományi hitelesítés nem működik a problémák elhárításához. <br /> Azure valósítja meg a környezetben való fizikai hozzáférés szükséges hozzáférési mechanizmusai helyi hozzáféréshez többtényezős hitelesítést. Az Azure adatközpontjaiban belül a rendszer az összes Azure infrastruktúra-rendszereknek tartalmazó belül helyiségekben különböző fizikai biztonsági mechanizmusok, beleértve a követelmény a vállalati intelligens kártya badging hozzáférés és a biometrikus eszközök segítségével korlátozódnak. Mindkét űrlapos hitelesítés szükség a fizikai hozzáférés az Azure-adatközpontban colocations belépési ponton. |


 ### <a name="nist-800-53-control-ia-2-4"></a>A NIST 800-53 vezérlő IA-2 (4)

#### <a name="identification-and-authentication-organizational-users--local-access-to-non-privileged-accounts"></a>Azonosítási és hitelesítési (szervezeti felhasználók) |} Helyi hozzáférési rendszerjogosultsággal nem rendelkező fiókok

**IA-2 (4)** a információk rendszer megvalósítja a többtényezős hitelesítés rendszerjogosultsággal nem rendelkező fiókok helyi elérésére.

**Feladatkörök:**`Azure Only`

|||
|---|---|
| **Ügyfél** | Ügyfél nem rendelkeznek helyi hozzáférni semmilyen rendszer erőforráshoz az Azure adatközpontjaiban. |
| **A szolgáltató (Microsoft Azure)** | A Microsoft Azure használják a Microsoft Azure Government személyzet rendszerjogosultságú fiókokhoz a Microsoft Azure Government figyelembe veszi. Többtényezős hitelesítés megvalósítása az összes Microsoft Azure Government személyzet fiók intelligens kártyák és PIN-kód, köztük a helyi hozzáférési. |


 ### <a name="nist-800-53-control-ia-2-5"></a>A NIST 800-53 vezérlő IA-2 (5)

#### <a name="identification-and-authentication-organizational-users--group-authentication"></a>Azonosítási és hitelesítési (szervezeti felhasználók) |} Csoport-hitelesítés

**IA-2 (5)** a szervezet megköveteli az egyéni felhasználók számára, ha egy csoport hitelesítő egyedi hitelesítők hitelesíteni.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek megosztott/csoport fiókok engedélyezve vannak a tervezetének által üzembe helyezett erőforrás. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-2-8"></a>A NIST 800-53 vezérlő IA-2 (8)

#### <a name="identification-and-authentication-organizational-users--network-access-to-privileged-accounts---replay-resistant"></a>Azonosítási és hitelesítési (szervezeti felhasználók) |} Kiemelt jogosultságú fiókok - ismétléses ellen védett hálózati hozzáférést

**IA-2 (8)** a információk rendszer megvalósítja a visszajátszás-ellenálló hitelesítési mechanizmusokkal hálózati hozzáférésű fiókokhoz.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének által telepített erőforrásokhoz való hozzáférést a beépített Kerberos-funkciók Azure Active Directory, az Active Directory és a Windows operációs rendszer által védett a ismétléses támadások. A Kerberos-hitelesítés, az ügyfél által küldött a hitelesítő további adatokat tartalmaz, például egy titkosított IP-listájában, a ügyfél időbélyegeket és a jegy élettartamát. Ha a rendszer játssza vissza a csomagot, a Timestamp típusú be van jelölve. Ha a Timestamp típusú korábbi, mint vagy ugyanaz, mint a korábbi hitelesítő, a csomag elutasítva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-2-9"></a>A NIST 800-53 vezérlő IA-2 (9)

#### <a name="identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts---replay-resistant"></a>Azonosítási és hitelesítési (szervezeti felhasználók) |} Hálózati hozzáférés rendszerjogosultsággal nem rendelkező fiókok - ismétléses ellenálló

**IA-2 (9)** a információk rendszer megvalósítja a visszajátszás-ellenálló hitelesítési mechanizmusok rendszerjogosultsággal nem rendelkező fiókok hálózati hozzáféréshez.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének által telepített erőforrásokhoz való hozzáférést a beépített Kerberos-funkciók Azure Active Directory, az Active Directory és a Windows operációs rendszer által védett a ismétléses támadások. A Kerberos-hitelesítés, az ügyfél által küldött a hitelesítő további adatokat tartalmaz, például egy titkosított IP-listájában, a ügyfél időbélyegeket és a jegy élettartamát. Ha a rendszer játssza vissza a csomagot, a Timestamp típusú be van jelölve. Ha a Timestamp típusú korábbi, mint vagy ugyanaz, mint a korábbi hitelesítő, a csomag elutasítva. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-2-11"></a>A NIST 800-53 vezérlő IA-2 (11)

#### <a name="identification-and-authentication-organizational-users--remote-access----separate-device"></a>Azonosítási és hitelesítési (szervezeti felhasználók) |} Távelérés - különálló eszköz

**IA-2 (11)** a információk rendszer megvalósítja a többtényezős hitelesítést a távelérés szolgáltatás kiemelt és rendszerjogosultsággal nem rendelkező fiókok, hogy hozzáférjenek a rendszer külön eszköz által biztosított egyik tényező, és az eszköz megfelel-e [ Hozzárendelés: mechanizmus követelmények szervezet által meghatározott erősségével].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős végrehajtási többtényezős hitelesítést a távoli ügyfél telepített erőforrások eléréséhez. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-2-12"></a>A NIST 800-53 vezérlő IA-2 (12)

#### <a name="identification-and-authentication-organizational-users--acceptance-of-piv-credentials"></a>Azonosítási és hitelesítési (szervezeti felhasználók) |} A Piv hitelesítő adatok elfogadása

**IA-2 (12)** az információs rendszer fogad el, és elektronikus úton ellenőrzi a személyes identitás ellenőrzése (PIV) hitelesítő adatokat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél fogad, és a személyes identitás ellenőrzése (PIV) hitelesítő adatok ellenőrzésének felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-3"></a>A NIST 800-53 vezérlő IA-3

#### <a name="device-identification-and-authentication"></a>Eszköz azonosítása és hitelesítése

**IA-3** az információs rendszer egyedileg azonosítja, és ezzel hitelesíti [hozzárendelés: szervezet által meghatározott specifikus és/vagy eszköztípusokat] létrehozó előtt egy [kijelölés (egy vagy több): helyi; távoli; hálózati] kapcsolat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A felhasználói eszköz azonosítási és hitelesítési végrehajtási felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-4a"></a>A NIST 800-53 vezérlő IA-4.a

#### <a name="identifier-management"></a>Felügyeleti azonosítója

**IA-4.a** a szervezet adatokat rendszer azonosítók felügyel fogadása az engedélyezési [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök] hozzárendelése egy személy, csoport, a szerepkör vagy az eszköz azonosítója.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felhasználói erőforrások (például egyéni felhasználók, csoportok, szerepkörök és eszközök) azonosítóinak felügyeletéért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-4b"></a>A NIST 800-53 vezérlő IA-4.b

#### <a name="identifier-management"></a>Felügyeleti azonosítója

**IA-4.b** a szervezet adatokat rendszer azonosítók kezeli, amely azonosítja az egy adott azonosítót kiválasztásával csoport, a szerepkör vagy az eszköz.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének megadását kéri az ügyfél által megadott készlet az egyes egyedi fiókok üzembe helyezése során.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-4c"></a>A NIST 800-53 vezérlő IA-4.c

#### <a name="identifier-management"></a>Felügyeleti azonosítója

**IA-4.c** a szervezet adatokat rendszer azonosítók kezeli a azonosítót rendel a megfelelő személy, a csoport, a szerepkör vagy az eszköz.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felhasználói erőforrások (például egyéni felhasználók, csoportok, szerepkörök és eszközök) azonosítóinak felügyeletéért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-4d"></a>A NIST 800-53 vezérlő IA-4.d

#### <a name="identifier-management"></a>Felügyeleti azonosítója

**IA-4.d** a szervezetem kezeli információk rendszer azonosítók azonosítók használatának letiltásával [hozzárendelés: szervezet által meghatározott időszak].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Active Directory és a Windows operációs rendszer helyi fiókokhoz rendelt egyedi biztonsági azonosítót (SID). Az Azure Active Directory-fiókok vannak hozzárendelve egy globálisan egyedi azonosítóra irányul. Ezek egyedi azonosítók használata nem újbóli tekintetében. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-4e"></a>A NIST 800-53 vezérlő IA-4.e

#### <a name="identifier-management"></a>Felügyeleti azonosítója

**IA-4.e** A szervezet adatokat rendszer azonosítók felügyel letiltása után a azonosítója [hozzárendelés: szervezet által meghatározott tétlen időszak].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének egy ütemezett feladatot az Active Directory automatikusan letiltja a fiókok tétlen 35 nap elteltével valósítja meg. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-4-4"></a>A NIST 800-53 vezérlő IA-4 (4)

#### <a name="identifier-management--identify-user-status"></a>Azonosító felügyeleti |} Felhasználói állapot azonosítása

**IA-4 (4)** a szervezet egyedi azonosítók kezeli úgy, hogy minden egyes személy, amely egyedileg azonosítja [hozzárendelés: szervezet által meghatározott jellemző egyéni állapot azonosító].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure Active Directory és az Active Directory támogatás azt jelöli, alvállalkozói, szállítók, és más felhasználó alkalmazza azok azonosítók elnevezési konvenciókat használja. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-5a"></a>NIST 800-53 Control IA-5.a

#### <a name="authenticator-management"></a>Felügyeleti hitelesítő

**IA-5.a** a szervezetem kezeli a rendszer hitelesítő adatokat ellenőrzésével, a kezdeti hitelesítő terjesztési, az egyéni identitás, csoport, szerepkör vagy a hitelesítő készülék részeként.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hitelesítők felügyeletéért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-5b"></a>A NIST 800-53 vezérlő IA-5.b

#### <a name="authenticator-management"></a>Felügyeleti hitelesítő

**IA-5.b** a szervezet kezeli a rendszer hitelesítő adatokat úgy, hogy a szervezet által meghatározott hitelesítők kezdeti hitelesítő tartalom létrehozásáról.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A tervezetének által létrehozott fiókok Tartalomtallózó kezdeti hitelesítő ellenőrzi a telepítés során az ügyfél által megadott IA-5 (1) a megadott követelményeknek.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-5c"></a>A NIST 800-53 vezérlő IA-5.c

#### <a name="authenticator-management"></a>Felügyeleti hitelesítő

**IA-5.c** a szervezetem kezeli a rendszer hitelesítő adatokat, hitelesítők rendelkezik-e elegendő erőssége felhasználásra mechanizmus biztosításával.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A hitelesítők használt a tervezetének igazodhat követelményei erőssége FedRAMP követelményei szerint. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-5d"></a>A NIST 800-53 vezérlő IA-5.d

#### <a name="authenticator-management"></a>Felügyeleti hitelesítő

**IA-5.d** a szervezet kezeli a rendszer hitelesítő adatokat úgy, hogy és végrehajtása során felügyeleti eljárások kezdeti hitelesítő terjesztéshez, elveszett vagy sérült vagy sérült hitelesítők, és visszavonása hitelesítők.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hitelesítők felügyeletéért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-5e"></a>A NIST 800-53 vezérlő IA-5.e

#### <a name="authenticator-management"></a>Felügyeleti hitelesítő

**IA-5.e** A szervezet kezeli a rendszer hitelesítő adatokat úgy, hogy hitelesítő adatokat a rendszer telepítés előtti alapértelmezett tartalmának módosítása.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az alapértelmezett a tervezetének összetevőihez összes hitelesítők módosítottak. Hitelesítők ügyfél által megadott alatt is ez a megoldás központi telepítését. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-5f"></a>A NIST 800-53 vezérlő IA-5.f

#### <a name="authenticator-management"></a>Felügyeleti hitelesítő

**IA-5.f** a szervezet kezeli a rendszer hitelesítő adatokat úgy, hogy minimális és maximális élettartama korlátozások és újbóli feltételeket hitelesítők számára.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hitelesítők felügyeletéért felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-5g"></a>A NIST 800-53 vezérlő IA-5.g

#### <a name="authenticator-management"></a>Felügyeleti hitelesítő

**IA-5.g** a szervezet kezeli a rendszer hitelesítő adatokat úgy, hogy hitelesítők módosítása vagy frissítése [hozzárendelés: szervezet által meghatározott időszakra vonatkozó hitelesítő típus szerint].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének, amelyhez az összes telepített virtuális gépek tartományhoz csatlakoztatott tartományvezérlő telepítését. A csoportházirend létrejött, és úgy konfigurálva, hogy a jelszó élettartama korlátozások (60 nap) megvalósítása. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-5h"></a>A NIST 800-53 vezérlő IA-5.h

#### <a name="authenticator-management"></a>Felügyeleti hitelesítő

**IA-5.h** a szervezet kezeli a rendszer hitelesítő adatokat úgy, hogy hitelesítő tartalom védelme a jogosulatlan és módosítását.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének valósítja meg a Key Vault szolgáltatás hitelesítő tartalom védelme a jogosulatlan és módosítását. A következő hitelesítők Key Vault vannak tárolva: az Azure-telepítés fiók, a virtuális gép rendszergazdai jelszót, az SQL Server szolgáltatásfiók jelszavát a jelszó. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-5i"></a>A NIST 800-53 vezérlő IA-5.i

#### <a name="authenticator-management"></a>Felügyeleti hitelesítő

**IA-5.i** a szervezetem kezeli a rendszer hitelesítő adatokat azzal, hogy érvénybe egyéni felhasználók számára, és rendelkező eszközök megvalósításához, konkrét biztonsági megvédi hitelesítők védelme érdekében.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének valósítja meg a Key Vault szolgáltatás hitelesítő tartalom védelme a jogosulatlan és módosítását. A következő hitelesítők Key Vault vannak tárolva: az Azure-telepítés fiók, a virtuális gép rendszergazdai jelszót, az SQL Server szolgáltatásfiók jelszavát a jelszó. Key Vault hardveres biztonsági modulokkal (HSM) védett kulcsokkal titkosítja a kulcsok és titkos kulcsokat (például a hitelesítési kulcsokat, tárfiókok kulcsait, az adattitkosítási kulcsokat és jelszavak). |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-5j"></a>A NIST 800-53 vezérlő IA-5.j

#### <a name="authenticator-management"></a>Felügyeleti hitelesítő

**IA-5.j** a szervezetem kezeli a rendszer hitelesítő adatokat hitelesítők csoport vagy szerepkör fiókok módosításával, amikor azok tagsági fiókok módosításokat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Nincsenek megosztott/csoport fiókok engedélyezve vannak a tervezetének által üzembe helyezett erőforrás. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-1a"></a>A NIST 800-53 vezérlő IA-5 (1) sémaszintjén

#### <a name="authenticator-management--password-based-authentication"></a>Felügyeleti hitelesítő |} Jelszó-alapú hitelesítés

**IA-5 (1) sémaszintjén** az információk rendszerhez, jelszóalapú hitelesítési érvénybe lépteti a jelszó minimális összetettsége [hozzárendelés: nagybetűk, a karakterek számát, a szervezet által meghatározott követelmények kombinálhatók-nagybetűk, amelyben a betűk, számok és speciális karakterek, beleértve a minimális követelmények az egyes].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének, amelyhez az összes telepített virtuális gépek tartományhoz csatlakoztatott tartományvezérlő telepítését. A csoportházirend létrejött, és kényszeríteni a virtuális gép helyi fiókok és az AD-fiókok jelszava foglalt bonyolultsági követelményeknek.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-1b"></a>A NIST 800-53 vezérlő IA-5 (1) .b

#### <a name="authenticator-management--password-based-authentication"></a>Felügyeleti hitelesítő |} Jelszó-alapú hitelesítés

**IA-5 (1) .b** az információk rendszerhez, jelszóalapú hitelesítési érvénybe lépteti legalább a következő számú módosított karakterek új jelszavak létrehozásakor: [hozzárendelés: szervezet által megadott szám].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős alkalmazó jelszóalapú hitelesítési ügyfél telepített erőforrások belül. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-1c"></a>A NIST 800-53 vezérlő IA-5 (1) .c

#### <a name="authenticator-management--password-based-authentication"></a>Felügyeleti hitelesítő |} Jelszó-alapú hitelesítés

**IA-5 (1) .c** az információk rendszerhez, jelszóalapú hitelesítési tárolja, és továbbítja a jelszavakat csak titkosítással védett.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure Directory annak biztosítására szolgál, hogy a jelszó titkosítással védett tárolt és továbbított adatok köre. Az Active Directory és a telepített Windows virtuális gépek a helyileg tárolt jelszavak automatikusan kivonatolja a beépített biztonsági funkcióinak részeként. Távoli asztal hitelesítési munkamenetek biztosított TLS használatával hitelesítők védelmének átvitelekor. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-1d"></a>A NIST 800-53 vezérlő IA-5 (1) .d

#### <a name="authenticator-management--password-based-authentication"></a>Felügyeleti hitelesítő |} Jelszó-alapú hitelesítés

**IA-5 (1) .d** az információk rendszerhez, jelszóalapú hitelesítési érvénybe lépteti a jelszó minimális és maximális élettartama korlátozásait [hozzárendelés: minimális, maximális élettartama-élettartam-szervezet által meghatározott számát].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének, amelyhez az összes telepített virtuális gépek tartományhoz csatlakoztatott tartományvezérlő telepítését. A csoportházirend létrejött, és kényszeríteni, hogy a minimális (1 nap) és (60 nap) maximális élettartama korlátozásai helyi fiókjait és AD-fiókok korlátozásait. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-1e"></a>A NIST 800-53 vezérlő IA-5 (1) .e

#### <a name="authenticator-management--password-based-authentication"></a>Felügyeleti hitelesítő |} Jelszó-alapú hitelesítés

**IA-5 (1) .e** Az információk rendszerhez, jelszóalapú hitelesítési tiltja, hogy a jelszó újbóli [hozzárendelés: szervezet által megadott szám] generációt.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének, amelyhez az összes telepített virtuális gépek tartományhoz csatlakoztatott tartományvezérlő telepítését. A csoportházirend létrehozott, és kényszeríteni újbóli feltételek (24 jelszó) a helyi fiókok és Hirdetési fiókokra vonatkozó korlátozásokat. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-1f"></a>A NIST 800-53 vezérlő IA-5 (1) .f

#### <a name="authenticator-management--password-based-authentication"></a>Felügyeleti hitelesítő |} Jelszó-alapú hitelesítés

**IA-5 (1) .f** adatokat a rendszer, jelszóalapú hitelesítés lehetővé teszi, hogy a rendszer bejelentkezések egy azonnali módosítását, hogy állandó jelszó ideiglenes jelszó használatát.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az Azure Active Directory információkat a hálózati vezérlő hozzáférés kezelésére szolgál. Amikor először hozza létre egy fiókot, vagy egy ideiglenes jelszót jön létre, Azure Active Directory alkalmaznak a szükséges, hogy a felhasználó módosíthatja a jelszót a következő bejelentkezésekor. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-2a"></a>A NIST 800-53 vezérlő IA-5 (2) sémaszintjén

#### <a name="authenticator-management--pki-based-authentication"></a>Felügyeleti hitelesítő |} PKI-alapú hitelesítés

**IA-5 (2) sémaszintjén** adatokat a rendszer, a PKI-alapú hitelesítéshez ellenőrzi a tanúsítványok hozhat létre, és a tanúsítványlánc egy elfogadott megbízhatósági kapcsolati alappal, beleértve a tanúsítványok állapotára vonatkozó információkat ellenőrzése a ellenőrzése.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél PKI-alapú hitelesítés belül ügyfél telepített erőforrások alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-2b"></a>A NIST 800-53 vezérlő IA-5 (2) .b

#### <a name="authenticator-management--pki-based-authentication"></a>Felügyeleti hitelesítő |} PKI-alapú hitelesítés

**IA-5 (2) .b** információs rendszer PKI-alapú hitelesítés érvénybe lépteti a jogosult a megfelelő titkos kulcsnak a hozzáférést.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél PKI-alapú hitelesítés belül ügyfél telepített erőforrások alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-2c"></a>A NIST 800-53 vezérlő IA-5 (2) .c

#### <a name="authenticator-management--pki-based-authentication"></a>Felügyeleti hitelesítő |} PKI-alapú hitelesítés

**IA-5 (2) .c** információkat a rendszer, a PKI-alapú hitelesítés van leképezve a hitelesített identitást a az egyéni fiókot vagy csoportot.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél PKI-alapú hitelesítés belül ügyfél telepített erőforrások alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-2d"></a>A NIST 800-53 vezérlő IA-5 (2) .d

#### <a name="authenticator-management--pki-based-authentication"></a>Felügyeleti hitelesítő |} PKI-alapú hitelesítés

**IA-5 (2) .d** adatokat a rendszer, a PKI-alapú hitelesítéshez valósítja meg a helyi gyorsítótárba visszavonási adatkészleteket elérési felderítés és az ellenőrzés esetén nem tudja elérni a visszavont tanúsítványok adatait a hálózaton keresztül.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél PKI-alapú hitelesítés belül ügyfél telepített erőforrások alkalmazó felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-3"></a>A NIST 800-53 vezérlő IA-5 (3)

#### <a name="authenticator-management--in-person-or-trusted-third-party-registration"></a>Felügyeleti hitelesítő |} Külső regisztrációja személyes vagy megbízható

**IA-5 (3)** a szervezet megköveteli, hogy a regisztrációs fogadásához feldolgozni [hozzárendelés: és/vagy a szervezet által meghatározott típusú adott hitelesítők] végezni [kijelölés: személy; megbízható harmadik fél] előtt [hozzárendelés: szervezet által meghatározott regisztrációszolgáltató] az engedélyezési által [hozzárendelés: szervezet által meghatározott csoporthoz vagy a szerepkörök].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hitelesítők regisztrálása felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-4"></a>A NIST 800-53 vezérlő IA-5 (4)

#### <a name="authenticator-management--automated-support--for-password-strength-determination"></a>Felügyeleti hitelesítő |} Jelszó erőssége meghatározása automatizált támogatása

**IA-5 (4)** a szervezet funkcióit használja annak meghatározására, hogy ha a jelszó hitelesítők megfelelően szigorú kielégítéséhez automatizált eszközök [hozzárendelés: szervezet által meghatározott követelményeknek].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Felhasználói fiókok telepítik a tervezetének közé tartoznak az AD és a helyi felhasználói fiókokhoz. Mindkét esetben adja meg a létrehozott jelszó követelményeket, hogy hozzon létre egy kezdeti jelszót, valamint a jelszómódosítást során kényszeríti-mechanizmust. Az Azure Active Directory az az automatizált eszköz alkalmazott annak meghatározására, hogy ha a jelszó hitelesítők elég erős a jelszó hosszát, összetettségét, elforgatás és élettartama korlátozásait a meglévő IA-5 (1) kielégítéséhez. Az Azure Active Directory biztosítja, hogy a létrehozásakor a jelszó hitelesítő erőssége megfelel ezeknek a szabványoknak. Ez a megoldás központi telepítéséhez használt felhasználói által megadott jelszavak jelszó erőssége követelményeinek megfelelően be van jelölve. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-6"></a>A NIST 800-53 vezérlő IA-5 (6)

#### <a name="authenticator-management--protection-of-authenticators"></a>Felügyeleti hitelesítő |} Hitelesítők védelme

**IA-5 (6)** a szervezet a Biztonság kategória az információ, amelyre a hitelesítő hatékonysága lehetővé teszi access arányos hitelesítők védelmet nyújt.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős hitelesítők védelme. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-7"></a>A NIST 800-53 vezérlő IA-5 (7)

#### <a name="authenticator-management--no-embedded-unencrypted-static-authenticators"></a>Felügyeleti hitelesítő |} Nem beágyazott titkosítatlan statikus hitelesítők

**IA-5 (7)** a szervezet biztosítja, hogy titkosítatlan statikus hitelesítők nem beágyazott alkalmazások vagy a hozzáférés parancsfájlok vagy a funkcióbillentyűket tárolt.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A titkosítatlan statikus hitelesítők nincs használatban az alkalmazásokba beágyazott parancsfájlok elérését, vagy a tervezetének által telepített funkcióbillentyűk. Bármely olyan parancsprogram vagy alkalmazás használ egy hitelesítő hívást indít az Azure Key Vault tároló minden használat előtt. Az Azure Key Vault tárolók eléréséhez naplózott, ami lehetővé teszi, hogy ez a Tiltás megsértésének észlelése szolgáltatásfiók használata az Azure Key Vault tároló megfelelő hívása nélkül egy rendszerhez való hozzáférés. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-8"></a>A NIST 800-53 vezérlő IA-5 (8)

#### <a name="authenticator-management--multiple-information-system-accounts"></a>Felügyeleti hitelesítő |} Több információk a System fiók

**IA-5 (8)** a szervezet valósít meg [hozzárendelés: szervezet által meghatározott biztonsági óvintézkedéseket] biztonsági sérülése miatt több információs rendszerekkel használt fiókok, amelyek egyéni kockázatának kezelésére.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hivatkozhatnak vállalati szintű biztonsági óvintézkedéseket rendelkező fiókok több rendszeren személyeket kockázatának kezelésére. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-11"></a>A NIST 800-53 vezérlő IA-5 (11)

#### <a name="authenticator-management--hardware-token-based-authentication"></a>Felügyeleti hitelesítő |} Hardver jogkivonat-alapú hitelesítés

**IA-5 (11)** adatokat a rendszer, a hardver jogkivonat-alapú hitelesítéshez, alkalmazza a mechanizmusok, amelyek megfelelnek [hozzárendelés: token szolgáltatásminőségi szervezet által meghatározott követelményeinek].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél hardver jogkivonat-alapú hitelesítés minőségi megfelelnek a alkalmazó mechanizmusok felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-5-13"></a>A NIST 800-53 vezérlő IA-5 (13)

#### <a name="authenticator-management--expiration-of-cached-authenticators"></a>Felügyeleti hitelesítő |} A gyorsítótárazott hitelesítő lejáratát

**IA-5 (13)** az adatokat a rendszer nem engedélyezi az után gyorsítótárazott hitelesítők használata [hozzárendelés: szervezet által meghatározott időszak].

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Nincs a tervezetének által üzembe helyezett erőforrás konfigurált gyorsítótárazott hitelesítők használatának engedélyezése. A telepített virtuális gépek hitelesítéshez az szükséges, hogy a hitelesítő is meg kell adni a hitelesítés során. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-6"></a>A NIST 800-53 vezérlő IA-6

#### <a name="authenticator-feedback"></a>Hitelesítő visszajelzés

**IA-6** az adatokat a rendszer elrejti a visszajelzés hitelesítési adatok illetéktelen személyek által lehetséges kihasználásának/használatát az információk védelme érdekében a hitelesítési folyamat során.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Ez tervezetének által telepített erőforrásokhoz való hozzáférés távoli asztalon keresztül, és Windows-hitelesítés támaszkodik. A Windows-hitelesítés munkamenetek alapértelmezés elfedi a jelszavak, mikor adjon meg hitelesítési munkamenet során.  |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-7"></a>A NIST 800-53 vezérlő IA-7

#### <a name="cryptographic-module-authentication"></a>Titkosító moduljának hitelesítés

**IA-7** az információs rendszer valósítja meg, amelyek megfelelnek a követelményeinek Szövetségi törvényi, végrehajtó rendelések, irányelvek, házirendek, szabványok, szabványok és ilyen útmutatás kriptográfiai modulra hitelesítési mechanizmusok hitelesítés.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Windows-hitelesítést, a távoli asztal és a BitLocker a tervezetének alkalmazottja. Ezek az összetevők beállítható úgy, hogy a titkosítási modulok FIPS 140 érvényesítése támaszkodnak. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ## <a name="nist-800-53-control-ia-8"></a>A NIST 800-53 vezérlő IA-8

#### <a name="identification-and-authentication-non-organizational-users"></a>Azonosítási és hitelesítési (nem szervezeti felhasználók)

**IA-8** az információs rendszer egyedileg azonosítja, és ezzel hitelesíti a nem szervezeti felhasználók (vagy nem szervezeti felhasználók nevében eljáró folyamatok).

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | A felhasználói azonosító és az ügyfél telepített erőforrások elérése nem szervezeti felhasználók hitelesítéséhez felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-8-1"></a>A NIST 800-53 vezérlő IA-8 (1)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-piv-credentials-from-other-agencies"></a>Azonosítási és hitelesítési (nem szervezeti felhasználók) |} Piv hitelesítő adatait más szervezetek elfogadása

**IA-8 (1)** az információs rendszer fogad el, és elektronikus úton ellenőrzi az egyéb Szövetségi szerveinek személyes identitás ellenőrzése (PIV) hitelesítő adatokat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős elfogadása, valamint más szövetségi szervezetek által kibocsátott személyes identitás ellenőrzése (PIV) hitelesítő adatok ellenőrzése. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-8-2"></a>A NIST 800-53 vezérlő IA-8 (2)

#### <a name="identification-and-authentication-non-organizational-users--acceptance-of-third-party-credentials"></a>Azonosítási és hitelesítési (nem szervezeti felhasználók) |} A külső hitelesítő adatok elfogadása

**IA-8 (2)** az információs rendszer fogad FICAM jóváhagyott csak külső hitelesítő adatokat.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél csak a külső hitelesítő adatokat a Szövetségi identitás, a hitelesítő adatok és a hozzáférés-kezelés (FICAM) megbízható keretrendszer megoldások kezdeményezés által jóváhagyott elfogadása felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-8-3"></a>A NIST 800-53 vezérlő IA-8 (3)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-approved-products"></a>Azonosítási és hitelesítési (nem szervezeti felhasználók) |} Ficam jóváhagyott termékek

**IA-8 (3)** a szervezet csak FICAM jóváhagyott információk rendszerösszetevők a védett [hozzárendelés: szervezet által megadott információk rendszerek] külső hitelesítő adatok fogadására.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél felelős alkalmazó csak Szövetségi azonosítóját, hitelesítő adat közül, és Access Management (FICAM) megbízható keretrendszer megoldások kezdeményezésére jóváhagyott külső hitelesítő adatok elfogadása erőforrásait. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |


 ### <a name="nist-800-53-control-ia-8-4"></a>A NIST 800-53 vezérlő IA-8 (4)

#### <a name="identification-and-authentication-non-organizational-users--use-of-ficam-issued-profiles"></a>Azonosítási és hitelesítési (nem szervezeti felhasználók) |} A profilok Ficam által kiállított használata

**IA-8 (4)** FICAM által kiállított profilok az információs rendszer megfelel-e.

**Feladatkörök:**`Customer Only`

|||
|---|---|
| **Ügyfél** | Az ügyfél a Szövetségi identitás, a hitelesítő adatok és a hozzáférés-kezelés (FICAM) megbízható keretrendszer megoldások kezdeményezésére által kibocsátott profilok megfelelő felelős. |
| **A szolgáltató (Microsoft Azure)** | Nem alkalmazható |
