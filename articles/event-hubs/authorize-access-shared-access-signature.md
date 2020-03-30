---
title: Hozzáférés engedélyezése megosztott hozzáférésű aláírással az Azure Event Hubs-ban
description: Ez a cikk az Azure Event Hubs-erőforrásokhoz való hozzáférés engedélyezéséről nyújt tájékoztatást megosztott hozzáférésű aláírások (SAS) használatával.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: bdb1896f8a40c6de21ae76b536bfccec316341cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992795"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz megosztott hozzáférésű aláírásokkal
A megosztott hozzáférésű aláírás (SAS) lehetővé teszi, hogy korlátozott hozzáférést biztosítson az Event Hubs névtérerőforrásaihoz. A SAS engedélyezési szabályok alapján védi az Event Hubs-erőforrásokhoz való hozzáférést. Ezek a szabályok vagy névtérben, vagy egy entitáson (eseményközpontban vagy témakörben) vannak konfigurálva. Ez a cikk áttekintést nyújt a SAS-modellről, és áttekinti a SAS ajánlott eljárásokat.

## <a name="what-are-shared-access-signatures"></a>Mik azok a megosztott hozzáférésű aláírások?
A megosztott hozzáférésű aláírás (SAS) az engedélyezési szabályok alapján delegált hozzáférést biztosít az Event Hubs erőforrásaihoz. Az engedélyezési szabálynak neve van, adott jogokkal van társítva, és egy pár kriptográfiai kulcsot hordoz. A szabály nevét és kulcsát az Event Hubs-ügyfeleken keresztül vagy a saját kódjában sas-jogkivonatok létrehozásához használhatja. Az ügyfél ezután átadhatja a jogkivonatot az Event Hubs-nak a kért művelet engedélyezésének bizonyításához.

A SAS egy egyszerű jogkivonatokat használó jogcímalapú engedélyezési mechanizmus. A SAS használatával a kulcsok soha nem kerülnek át a vezetéken. A kulcsok olyan információk titkosítására szolgálnak, amelyeket a szolgáltatás később ellenőrizhet. A SAS hasonló a felhasználónévhez és jelszósémához, ahol az ügyfél azonnali hatállyal rendelkezik egy engedélyezési szabály nevével és egy megfelelő kulccsal. A SAS hasonló az összevont biztonsági modellhez, ahol az ügyfél egy korlátozott idejű és aláírt hozzáférési jogkivonatot kap egy biztonsági jogkivonat-szolgáltatásból anélkül, hogy az aláíró kulcs birtokába jutna.

> [!NOTE]
> Az Azure Event Hubs támogatja az Event Hubs-erőforrások engedélyezését az Azure Active Directory (Azure AD) használatával. Az Azure AD által visszaadott OAuth 2.0-s jogkivonatot használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a megosztott hozzáférésű aláírások (SAS) használatával. Az Azure AD-vel nincs szükség a jogkivonatok tárolására a kódban, és potenciális biztonsági rések et kockáztat.
>
> A Microsoft azt javasolja, hogy az Azure AD-t az Azure Event Hubs-alkalmazásokkal, ha lehetséges, használja. További információ: [Hozzáférés engedélyezése az Azure Event Hubs-erőforráshoz az Azure Active Directory használatával című témakörben.](authorize-access-azure-active-directory.md)

> [!IMPORTANT]
> A SAS (shared access signatures) jogkivonatok kritikus fontosságúak az erőforrások védelme érdekében. A részletesség biztosítása mellett a SAS hozzáférést biztosít az ügyfelek számára az Event Hubs-erőforrásokhoz. Ezeket nem szabad nyilvánosan megosztani. Ha megosztja, ha hibaelhárítási okokból szükséges, fontolja meg a naplófájlok csökkentett verzióját, vagy a SAS-tokenek törlését (ha vannak) a naplófájlokból, és győződjön meg arról, hogy a képernyőképek sem tartalmazzák a SAS-adatokat.

## <a name="shared-access-authorization-policies"></a>Megosztott hozzáférés-engedélyezési házirendek
Minden Event Hubs névtér és minden Event Hubs entitás (egy eseményközpont-példány vagy egy Kafka-témakör) rendelkezik egy szabályokból álló megosztott hozzáférés-engedélyezési szabályzattal. A névtér szintjén lévő házirend a névtérben lévő összes entitásra vonatkozik, függetlenül az egyéni házirendkonfigurációtól.
Minden egyes engedélyezési házirend-szabályesetében három adat közül dönthet: név, hatókör és jogok. A név egy egyedi név a hatókörben. A hatókör a kérdéses erőforrás URI-ja. Az Event Hubs névtér esetében a hatókör a teljesen minősített tartománynév `https://<yournamespace>.servicebus.windows.net/`(FQDN), például .

A házirendszabály által biztosított jogok a következők lehetnek:
- **Küldés** – Jogot ad arra, hogy üzeneteket küldjön az entitásnak
- **Listen** - Jogot ad arra, hogy meghallgassa vagy megkapja a entitást
- **Kezelés** – Jogot ad a névtér topológiájának kezelésére, beleértve az entitások létrehozását és törlését

> [!NOTE]
> A **Kezelés** jog tartalmazza a **Küldés** és **figyelés** jogokat.

Egy névtér vagy entitásházirend legfeljebb 12 megosztott hozzáférés-engedélyezési szabályt tarthat fenn, így helyet biztosítva a három szabálykészletnek, amelyek mindegyike lefedi az alapvető jogokat, valamint a Küldés és figyelés kombinációját. Ez a korlát kiemeli, hogy a SAS-házirendtároló nem felhasználói vagy szolgáltatásfiók-tároló. Ha az alkalmazásnak hozzáférést kell biztosítania az Event Hubs-erőforrásokhoz a felhasználó vagy a szolgáltatás identitása alapján, egy biztonsági jogkivonat-szolgáltatást kell megvalósítania, amely hitelesítési és hozzáférési ellenőrzés után SAS-jogkivonatokat ad ki.

Az engedélyezési szabály hoz egy **elsődleges** és egy **másodlagos kulcsot.** Ezek a kulcsok kriptográfiailag erős kulcsok. Ne veszítsd el őket, és ne szivárogtasd ki őket. Ezek mindig elérhetők lesznek az Azure Portalon. Használhatja a létrehozott kulcsok bármelyikét, és bármikor újragenerálhatja őket. Ha újragenerál vagy módosít egy kulcsot a házirendben, az adott kulcson alapuló, korábban kiadott jogkivonatok azonnal érvénytelenné válnak. Az ilyen jogkivonatok alapján létrehozott folyamatos kapcsolatok azonban a jogkivonat lejáratáig továbbra is működni fognak.

Az Event Hubs névtér létrehozásakor a rendszer automatikusan létrehoz egy **RootManageSharedAccessKey** nevű házirendszabályt a névtérhez. Ez a házirend a teljes névtérhez **rendelkezik kezelési** engedélyekkel. Javasoljuk, hogy kezelje ezt a szabályt, mint egy rendszergazdai gyökérfiókot, és ne használja az alkalmazásban. További szabályzatszabályokat hozhat létre a **Konfigurálás** lapon a névtérhez a portálon, a PowerShell vagy az Azure CLI segítségével.

## <a name="best-practices-when-using-sas"></a>A SAS használatával kapcsolatos ajánlott eljárások
Ha megosztott hozzáférésű aláírásokat használ az alkalmazásokban, két lehetséges kockázattal kell tisztában lennie:

- Ha egy SAS kiszivárgott, bárki használhatja, aki beszerzi, ami potenciálisan veszélyeztetheti az Event Hubs erőforrásait.
- Ha egy ügyfélalkalmazásnak biztosított SAS lejár, és az alkalmazás nem tud új SAS-t lekérni a szolgáltatásból, akkor az alkalmazás működése akadályokba ütközhet.

A közös hozzáférésű aláírások használatára vonatkozó alábbi javaslatok segíthetnek a kockázatok csökkentésében:

- **Az ügyfelek szükség esetén automatikusan megújítják a SAS-t**: Az ügyfeleknek jóval a lejárat előtt meg kell újítaniuk a SAS-t, hogy időt biztosítson az újrapróbálkozásokra, ha a SAS-t biztosító szolgáltatás nem érhető el. Ha a SAS célja, hogy használni kell egy kis számú azonnali, rövid életű műveletek, amelyek várhatóan befejeződik a lejárati időn belül, akkor szükségtelen lehet, mivel a SAS várhatóan nem újítható meg. Azonban, ha az ügyfél, amely rendszeresen kéri keresztül SAS, majd a lejárati lehetőség jön szóba. A legfontosabb szempont az, hogy egyensúlyt kell teremteni annak szükségességét, hogy a SAS rövid életű legyen (amint azt korábban említettük) annak biztosításával, hogy az ügyfél elég korán kérje a megújítást (hogy elkerüljék a SAS sikeres megújítása előtt lejáró zavarait).
- **Legyen óvatos a SAS kezdési időpontjával:** Ha a SAS kezdési időpontját **mosta**állítja be, akkor az óraeltérés (az aktuális idő különböző gépek szerinti eltérései) miatt a hibák időszakosan figyelhetők meg az első néhány percben. Általában állítsa be a kezdési időt legalább 15 percre a múltban. Vagy ne állítsa be egyáltalán, ami minden esetben azonnal érvényessé teszi. Ugyanez vonatkozik általában a lejárati idő is. Ne feledje, hogy bármilyen kérésre akár 15 perces óraeltérést is megkérhet. 
- **Legyen konkrét az elérendő erőforrással:** A biztonság ajánlott eljárása, hogy a felhasználó a minimálisan szükséges jogosultságokat. Ha egy felhasználónak csak olvasási hozzáférésre van szüksége egyetlen entitáshoz, majd olvasási hozzáférést kell biztosítania az adott entitáshoz, és nem kell olvasnia/írnia/törölnie az összes entitáshoz való hozzáférést. Ez is segít csökkenteni a kárt, ha a SAS veszélybe kerül, mert a SAS kevesebb erő a kezében a támadó.
- Ne mindig használja a **SAS-t:** Előfordulhat, hogy az Event Hubok egy adott művelethez kapcsolódó kockázatok meghaladják a SAS előnyeit. Az ilyen műveletekhez hozzon létre egy középső rétegű szolgáltatást, amely az üzleti szabályok érvényesítése, hitelesítése és naplózása után ír az Eseményközpontokba.
- **Mindig használja a HTTPs:** Mindig https sas létrehozásához vagy terjesztéséhez. Ha egy SAS http-n keresztül halad át, és elfogják, a fájlba kerülő fájltárst végző támadó képes olvasni a SAS-t, majd ugyanúgy használni, mint a hogy a szándékolt felhasználó rendelkezhet, potenciálisan veszélyeztetve a bizalmas adatokat, vagy lehetővé téve a rosszindulatú felhasználó általi adatsérülést.

## <a name="conclusion"></a>Összegzés
A hozzáférési jogosultsági aláírások megosztására vonatkozó engedélyek et az Ügyfélközpontok erőforrásainak biztosítása érdekében hasznosak. Ezek az Azure Event Hubs-ot használó bármely alkalmazás biztonsági modelljének létfontosságú részét képezik. Ha követi az ebben a cikkben felsorolt ajánlott eljárásokat, a SAS használatával nagyobb rugalmasságot biztosíthat az erőforrásokhoz való hozzáféréshez anélkül, hogy veszélyeztetné az alkalmazás biztonságát.

## <a name="next-steps"></a>További lépések
Lásd a következő kapcsolódó cikkeket: 

- [Az Azure-eseményközpontoknak az Azure Active Directory használatával történő hitelesítése](authenticate-application.md)
- [Felügyelt identitás hitelesítése az Azure Active Directoryval az Event Hubs-erőforrások eléréséhez](authenticate-managed-identity.md)
- [Az Azure Event Hubs-ra irányuló kérelmek hitelesítése megosztott hozzáférésű aláírásokkal](authenticate-shared-access-signature.md)
- [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz az Azure Active Directory használatával](authorize-access-azure-active-directory.md)


