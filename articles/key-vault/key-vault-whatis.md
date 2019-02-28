---
title: Mi az Azure Key Vault? | Microsoft Docs
description: Útmutató az Azure Key Vault megvédi a titkosítási kulcsok és titkos kódok, amelyek felhőalapú alkalmazásokat és szolgáltatásokat használja.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 48ac0c3efe74723099e87a77871aa1a78834efbd
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958532"
---
# <a name="what-is-azure-key-vault"></a>Mi az Azure Key Vault?

Titkosítási kulcsok használata a felhőalapú alkalmazások és szolgáltatások, és annak érdekében, hogy információt titkos kulcsok biztonságos. Az Azure Key Vault megvédi a kulcsok és titkos kulcsok. A Key Vault használata esetén hardveres biztonsági modulokban (HSM) védett kulcsokkal hitelesítési kulcsokat, a tárfiókkulcsok, az adattitkosítási kulcsokat, .pfx fájlok és jelszavak használatával titkosítsa.

Key Vault segít a következő problémákat megoldani:

- **Titkos kódok kezelése**: Tárolja biztonságosan, és szorosan való hozzáféréseket a jogkivonatokat, jelszavak, tanúsítványok, API-kulcsok és egyéb titkos adatait.
- **Kulcskezelés**: Hozzon létre, és kezelheti a titkosítási kulcsokat az adatok titkosításához. 
- **Tanúsítványkezelés az**: Üzembe helyezése, kezelése és üzembe helyezése az Azure és a belső csatlakoztatott erőforrásokhoz használt nyilvános és privát Secure Sockets Layer/Transport Layer Security (SSL/TLS) tanúsítványok. 
- **Titkos kulcsok a HSM-EK által támogatott Store**: Használja a szoftvert vagy a FIPS 140-2 2. szintje ellenőrzött HSM-kulcsok és titkok védelme érdekében.

## <a name="basic-concepts"></a>Alapfogalmak

Az Azure Key Vault egy titkos kulcsok biztonságos tárolására és hozzáférésére használható eszköz. Titkos kulcsnak számít minden olyan adat, amelynek a hozzáféréstét szigorúan korlátozni kívánja, például: API-kulcsok, jelszavak vagy tanúsítványok. Egy tároló egy logikai csoport a titkos kulcsok.

Az alábbiakban az egyéb fontos kifejezések:

- **Bérlő**: Egy bérlő a szervezet birtokolja és kezeli a Microsoft cloud services adott példányán. Leggyakrabban használt tekintse meg a szervezet az Azure és az Office 365 szolgáltatások körét.

- **Tároló tulajdonosa**: Egy tároló tulajdonos is hozzon létre egy kulcstartót, és teljes hozzáféréssel, és szabályozhatja azt. Emellett naplózást is beállíthat, amellyel naplózhatja a titkos kulcsok és a kulcsok elérését. A kulcsok életciklusát a rendszergazdák kezelhetik. Kiadhatnak új kulcsverziókat, biztonsági másolatokat készíthetnek, és elvégezhetik a kapcsolódó feladatokat.

- **Tároló fogyasztói**: Egy tároló fogyasztói műveleteket tudják végrehajtani a key vault belül az eszközöket, amikor a tároló tulajdonos ruházza fel a felhasználói hozzáférés. Az elérhető műveletek a kiosztott jogosultságoktól függnek.

- **Erőforrás**: Egy erőforrás egy olyan kezelhető elem, amely az Azure-on keresztül érhető el. Gyakori alkalmazási helyzetek a virtuális gép, tárfiók, webalkalmazás, adatbázis és virtuális hálózat. Vannak még sok más.

- **Erőforráscsoport**: Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A cég számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között.

- **Egyszerű szolgáltatás**: Azure-beli szolgáltatásnév egy biztonsági identitás, amely a felhasználó által létrehozott appok, szolgáltatások és automatizálási eszközök használata a meghatározott Azure-erőforrások eléréséhez. Képzelje el úgy, mint egy "felhasználói identitást" (felhasználónév és jelszó vagy tanúsítvány) egy adott szerepkörrel és szigorúan szabályzott engedélyekkel. Az általános identitásoktól eltérően a szolgáltatásnév csak konkrét feladatok végrehajtására szolgál. Ez növeli a biztonsági, ha csak az a felügyeleti feladataihoz szükséges minimális engedély szükséges szintet biztosít azt.

- [Az Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): Az Azure AD-bérlő az Active Directory szolgáltatás. Minden címtárhoz tartozik egy vagy több tartomány. Egy címtárhoz számos előfizetés tartozhat, de csak egyetlen bérlő.

- **Azure-bérlő azonosítója**: Egy bérlői azonosító egy egyedi módja egy Azure-előfizetésen belül egy Azure AD példányát.

- **Felügyelt identitások**: Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. Egyszerűbb a probléma megoldására azzal, hogy az Azure-szolgáltatások automatikusan felügyelt identitás az Azure ad-ben egy felügyelt identitás használata révén. Ezzel az identitással anélkül végezhet hitelesítést a Key Vaultban vagy bármely, Azure AD-hitelesítést támogató szolgáltatásban, hogy a hitelesítő adatokat a kódban kellene tárolnia. További információkért lásd az alábbi képen a [áttekintése az Azure-erőforrások felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md).

    ![Az Azure-erőforrások work felügyelt identitások ábrája](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Ehhez a Key Vault műveleteket, először azt hitelesítésre. A Key Vault hitelesítésre három módja van:

- [Felügyelt identitások az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview): Amikor telepít egy alkalmazást, az Azure-beli virtuális gépen, a virtuális gép, amely hozzáfér a Key Vault rendelhet az identitás. Szintén hozzárendelheti a kívánt identitások [más Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Ez a megközelítés előnye, hogy az alkalmazás vagy szolgáltatás nem kezelése elforgatási szögét az első titkos kulcsot. Az Azure automatikusan elforgatja az identitást. Ajánlott eljárásként azt javasoljuk ezt a megközelítést. 
- **Szolgáltatás egyszerű és a tanúsítvány**: Használhat egy egyszerű szolgáltatást, és egy társított tanúsítvány, amely rendelkezik Key Vaulthoz való hozzáférése. Ez a módszer nem ajánlott, mert az alkalmazás tulajdonosa vagy a fejlesztői elforgatása kell a tanúsítványt.
- **Egyszerű szolgáltatás és a titkos kulcs**: Bár a Key Vault való hitelesítéséhez használhatja az egyszerű szolgáltatás és a egy titkos kulcsot nem ajánlott. A rendszer-indításkori titkos kulcsot, amellyel hitelesíti a Key Vault automatikusan rotálhatja nehezen.


## <a name="key-vault-roles"></a>Key Vault-szerepkörök

Az alábbi táblázat segítségével jobban megértheti, hogyan segíti a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek kielégítését.

| Szerepkör | Probléma leírása | Az Azure Key Vault megoldása |
| --- | --- | --- |
| Azure-alkalmazásfejlesztő |"Szeretnék írni egy alkalmazást az Azure-ban, amely kulcsokat használ az aláíráshoz és titkosításhoz. De szeretném ezeket a kulcsokat, hogy az alkalmazásom úgy, hogy a megoldás egy földrajzilag elosztott alkalmazás. <br/><br/>Azt szeretném, hogy ezek a kulcsok és titkos kulcsok el legyenek látva védelemmel, anélkül, hogy meg kellene írnom a kódot. Azt is szeretném, hogy a kulcsokat és a titkos kulcsokat könnyedén használni lehessen az alkalmazásaimból, optimális teljesítmény mellett.” |√ A kulcsok tárolása tárolóban történik, és szükség esetén egy URI segítségével lehet előhívni őket.<br/><br/> √ A kulcsok számára az Azure biztosít védelmet az ipari szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulok segítségével.<br/><br/> √ A kulcsok feldolgozása hardveres biztonsági modulokban történik, amelyek ugyanazokban az Azure-adatközpontokban találhatók, mint az alkalmazások. Ez a módszer nagyobb megbízhatóságot és kisebb késést eredményez ahhoz képest, mintha a kulcsok egy külön helyen, például a helyszínen lennének. |
| Szolgáltatott szoftverek fejlesztője (SaaS-fejlesztő) |„Nem szeretnék felelősséget és esetleges felelősségre vonást vállalni az ügyfeleim bérlőinek kulcsaiért és titkos kulcsaiért. <br/><br/>Szeretném, ha az ügyfeleim saját maguk kezelnék a kulcsaikat, én pedig arra összpontosíthatnék, amihez leginkább értek: az alapvető szoftverfunkciók biztosítására.” |√ Az ügyfelek importálhatják a saját kulcsaikat az Azure rendszerbe, és kezelhetik őket. Amikor egy SaaS-alkalmazásnak titkosítási műveleteket ügyfelek kulcsainak használatával kell, a Key Vault ezeket a műveleteket, az alkalmazás nevében végzi. Az alkalmazás nem látja az ügyfelek kulcsait. |
| Biztonsági vezető (CSO) |„Szeretnék biztos lenni abban, hogy az alkalmazásaink a biztonságos kulcskezelés tekintetében megfelelnek a FIPS 140-2 2. szintje szerint ellenőrzött HSM-eknek. <br/><br/>Biztos szeretnék lenni abban is, hogy a cégünk kézben tartja a kulcsok életciklusát, valamint meg tudja figyelni a kulcshasználatot. <br/><br/>Továbbá – bár több Azure-szolgáltatást és -erőforrást is használunk – egyetlen helyről szeretném kezelni a kulcsokat az Azure rendszerén belül.” |√ A hardveres biztonsági modulokat a FIPS 140-2 2. szintje szerint ellenőrizték.<br/><br/>√ A Key Vault kialakításának köszönhetően a Microsoft nem tekintheti meg, illetve nem nyerheti ki a kulcsokat.<br/><br/>√ A kulcshasználatot közel valós időben naplózza rendszer.<br/><br/>√ A tároló egyetlen felületet biztosít függetlenül attól, hogy Ön hány tárolóval rendelkezik az Azure rendszerben, ezek mely régiókat támogatják, illetve mely alkalmazások használják őket. |

Azure-előfizetés birtokában bárki létrehozhat és használhat kulcstárolót. Bár a Key Vault elsősorban a fejlesztők és a biztonsági rendszergazdák, megvalósítva, és más Azure-szolgáltatásokat felügyelő a szervezet rendszergazdája kezeli. Például a rendszergazda bejelentkezhet az Azure-előfizetéssel, hozzon létre egy tárolót a szervezet, amelyben tárolni a kulcsokat, és felelős üzemeltetési feladatok az alábbiakhoz hasonló:

- A kulcsok vagy titkos kulcsok létrehozása és importálása
- A kulcsok vagy titkos kulcsok visszavonása, illetve törlése
- A felhasználók vagy alkalmazások feljogosítása a kulcstárolóhoz való hozzáférésre, hogy azután kezelhessék és használhassák a benne tárolt kulcsokat és titkos kulcsokat
- A kulcshasználat konfigurálása (például aláíráshoz vagy titkosításhoz)
- A kulcshasználat figyelése

Ez a rendszergazda ezután biztosítja a fejlesztők számára URI-k hívásához alkalmazásaikból. Ez a rendszergazda a biztonsági rendszergazda kulcshasználati naplózási információkat is biztosít. 

![Az Azure Key Vault működésének áttekintése][1]

A fejlesztők közvetlenül is kezelhetik a kulcsokat API-k használatával. További információkért tekintse meg a [Key Vault fejlesztői útmutatóját](key-vault-developers-guide.md).

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [a tároló biztonságos](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).
