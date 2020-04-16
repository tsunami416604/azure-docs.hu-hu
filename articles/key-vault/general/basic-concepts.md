---
title: Mi az Azure Key Vault? | Microsoft Docs
description: Ismerje meg, hogy az Azure Key Vault hogyan védi a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsokat és titkos kulcsokat.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 14eda137d386146d96b6b9aa54e1ed57021db19d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432137"
---
# <a name="azure-key-vault-basic-concepts"></a>Az Azure Key Vault alapfogalmai

Az Azure Key Vault egy titkos kulcsok biztonságos tárolására és hozzáférésére használható eszköz. Titkos kulcsnak számít minden olyan adat, amelynek a hozzáféréstét szigorúan korlátozni kívánja, például: API-kulcsok, jelszavak vagy tanúsítványok. A páncélterem a titkok logikus csoportja.

Itt vannak más fontos feltételek:

- **Bérlő**: A bérlő az a cég vagy intézmény, amely egy Microsoft-felhőszolgáltatás egy adott példányát birtokolja és kezeli. Leggyakrabban az Azure és az Office 365-szolgáltatások készletére hivatkozik egy szervezet számára.

- **Kulcstartó-tulajdonos**: Létrehozhat egy Key Vaultot, amely felett teljes körű hozzáféréssel és irányítással rendelkezik. Emellett naplózást is beállíthat, amellyel naplózhatja a titkos kulcsok és a kulcsok elérését. A kulcsok életciklusát a rendszergazdák kezelhetik. Kiadhatnak új kulcsverziókat, biztonsági másolatokat készíthetnek, és elvégezhetik a kapcsolódó feladatokat.

- **Kulcstartóhasználó**: A kulcstartóhasználó műveleteket hajthat végre a Key Vaultban található objektumokon, ha a kulcstartó-tulajdonos felruházta hozzáféréssel. Az elérhető műveletek a kiosztott jogosultságoktól függnek.

- **Erőforrás**: Az erőforrás egy olyan kezelhető elem, amely az Azure-on keresztül érhető el. Gyakori példák a virtuális gép, a tárfiók, a webalkalmazás, az adatbázis és a virtuális hálózat. Sokkal többen vannak.

- **Erőforráscsoport**: Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A cég számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között.

- **Egyszerű szolgáltatás:** Az Azure egyszerű szolgáltatás egy biztonsági identitás, amelyet a felhasználó által létrehozott alkalmazások, szolgáltatások és automatizálási eszközök használnak bizonyos Azure-erőforrások eléréséhez. Gondoljon rá úgy, mint egy "felhasználói identitásra" (felhasználónév és jelszó vagy tanúsítvány), amely egy adott szerepkörrel és szigorúan ellenőrzött engedélyekkel rendelkezik. Az általános identitásoktól eltérően a szolgáltatásnév csak konkrét feladatok végrehajtására szolgál. Növeli a biztonságot, ha csak azt a minimális jogosultsági szintet adja meg, amelyre a felügyeleti feladatok elvégzéséhez szüksége van.

- [Azure Active Directory (Azure AD)](../../active-directory/active-directory-whatis.md): Az Azure AD egy bérlő Active Directory-szolgáltatása. Minden címtárhoz tartozik egy vagy több tartomány. Egy címtárhoz számos előfizetés tartozhat, de csak egyetlen bérlő.

- **Azure-bérlő azonosítója**: A bérlőazonosító egy egyedi módszer az Azure AD-példány azonosítására az Azure-előfizetésen belül.

- **Felügyelt identitások:** Az Azure Key Vault lehetővé teszi a hitelesítő adatok és más kulcsok és titkos kulcsok biztonságos tárolásának módját, de a kódnak hitelesítenie kell magát a Key Vaultba azok lekéréséhez. A felügyelt identitás használata egyszerűbbé teszi a probléma megoldását azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosít az Azure AD-ben. Ezzel az identitással anélkül végezhet hitelesítést a Key Vaultban vagy bármely, Azure AD-hitelesítést támogató szolgáltatásban, hogy a hitelesítő adatokat a kódban kellene tárolnia. További információt az alábbi képen és az [Azure-erőforrások felügyelt identitásainak áttekintésében talál.](../../active-directory/managed-identities-azure-resources/overview.md)

    ![Az Azure-erőforrások felügyelt identitásainak működésének diagramja](../media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Hitelesítés
Bármilyen művelet et a Key Vault, először meg kell hitelesíteni azt. A Key Vaultban háromféleképpen lehet hitelesíteni:

- [Felügyelt identitások az Azure-erőforrások:](../../active-directory/managed-identities-azure-resources/overview.md)Ha egy alkalmazást üzembe helyez egy virtuális gépen az Azure-ban, identitást rendelhet a virtuális gép, amely hozzáfér a Key Vaulthoz. [Más Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md)is hozzárendelhet identitásokat. Ennek a megközelítésnek az az előnye, hogy az alkalmazás vagy szolgáltatás nem kezeli az első titok rotációját. Az Azure automatikusan elforgatja az identitást. Ezt a megközelítést ajánlott eljárásként javasoljuk. 
- **Egyszerű szolgáltatás és tanúsítvány:** Használhatja a szolgáltatás névszerint és a kapcsolódó tanúsítvány, amely hozzáfér a Key Vault. Nem javasoljuk ezt a módszert, mert az alkalmazás tulajdonosának vagy fejlesztőjének el kell forgatnia a tanúsítványt.
- **Egyszerű és titkos szolgáltatás:** Bár egy egyszerű szolgáltatás és egy titkos kulcs a Key Vault hitelesítéséhez, nem javasoljuk. Nehéz automatikusan elforgatni a bootstrap titkos, amely hitelesítésére használják a Key Vault.


## <a name="key-vault-roles"></a>Key Vault-szerepkörök

Az alábbi táblázat segítségével jobban megértheti, hogyan segíti a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek kielégítését.

| Szerepkör | Probléma leírása | Az Azure Key Vault megoldása |
| --- | --- | --- |
| Azure-alkalmazásfejlesztő |"Olyan azure-alkalmazást szeretnék írni, amely kulcsokat használ az aláíráshoz és a titkosításhoz. De azt akarom, hogy ezek a kulcsok külső alkalmazásból, hogy a megoldás alkalmas egy alkalmazás, amely földrajzilag elosztott. <br/><br/>Azt szeretném, hogy ezek a kulcsok és titkos kulcsok el legyenek látva védelemmel, anélkül, hogy meg kellene írnom a kódot. Azt is szeretném, hogy ezek a kulcsok és titkok könnyen használhatóak legyenek az alkalmazásaimból, optimális teljesítménnyel." |√ A kulcsok tárolása tárolóban történik, és szükség esetén egy URI segítségével lehet előhívni őket.<br/><br/> √ A kulcsok számára az Azure biztosít védelmet az ipari szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulok segítségével.<br/><br/> √ A kulcsok feldolgozása hardveres biztonsági modulokban történik, amelyek ugyanazokban az Azure-adatközpontokban találhatók, mint az alkalmazások. Ez a módszer nagyobb megbízhatóságot és kisebb késést eredményez ahhoz képest, mintha a kulcsok egy külön helyen, például a helyszínen lennének. |
| Szolgáltatott szoftverek fejlesztője (SaaS-fejlesztő) |"Nem akarom, hogy a felelősség vagy a potenciális felelősséget az ügyfeleim bérlői kulcsok és titkok. <br/><br/>Azt akarom, hogy az ügyfelek birtokolják és kezeljék a kulcsaikat, hogy arra koncentrálhassak, amihez a legjobban szolgálok, ami biztosítja az alapvető szoftverfunkciókat." |√ Az ügyfelek importálhatják a saját kulcsaikat az Azure rendszerbe, és kezelhetik őket. Ha egy SaaS-alkalmazásnak kriptográfiai műveleteket kell végrehajtania az ügyfelek kulcsainak használatával, a Key Vault ezeket a műveleteket az alkalmazás nevében hajtja végre. Az alkalmazás nem látja az ügyfelek kulcsait. |
| Biztonsági vezető (CSO) |"Azt akarom tudni, hogy alkalmazásaink megfelelnek a FIPS 140-2 Level 2 HSMs biztonságos kulcskezelés. <br/><br/>Biztos szeretnék lenni abban is, hogy a cégünk kézben tartja a kulcsok életciklusát, valamint meg tudja figyelni a kulcshasználatot. <br/><br/>És bár több Azure-szolgáltatást és -erőforrást használunk, a kulcsokat egyetlen helyről szeretném kezelni az Azure-ban." |√ A hardveres biztonsági modulokat a FIPS 140-2 2. szintje szerint ellenőrizték.<br/><br/>√ A Key Vault kialakításának köszönhetően a Microsoft nem tekintheti meg, illetve nem nyerheti ki a kulcsokat.<br/><br/>√ A kulcshasználatot közel valós időben naplózza rendszer.<br/><br/>√ A tároló egyetlen felületet biztosít függetlenül attól, hogy Ön hány tárolóval rendelkezik az Azure rendszerben, ezek mely régiókat támogatják, illetve mely alkalmazások használják őket. |

Azure-előfizetés birtokában bárki létrehozhat és használhat kulcstárolót. Bár a Key Vault a fejlesztők és a biztonsági rendszergazdák számára előnyös, a szervezet más Azure-szolgáltatásokat kezelő rendszergazdája valósíthatja meg és kezelheti. Ez a rendszergazda például bejelentkezhet egy Azure-előfizetéssel, létrehozhat egy tárolót a szervezet számára, amelyben kulcsokat tárolhat, majd felelős az alábbihoz hasonló üzemeltetési feladatokért:

- A kulcsok vagy titkos kulcsok létrehozása és importálása
- A kulcsok vagy titkos kulcsok visszavonása, illetve törlése
- A felhasználók vagy alkalmazások feljogosítása a kulcstárolóhoz való hozzáférésre, hogy azután kezelhessék és használhassák a benne tárolt kulcsokat és titkos kulcsokat
- A kulcshasználat konfigurálása (például aláíráshoz vagy titkosításhoz)
- A kulcshasználat figyelése

Ez a rendszergazda ezután megadja a fejlesztőkuridáknak, hogy az alkalmazásukból hívjanak. Ez a rendszergazda a kulcshasználat naplózásával kapcsolatos információkat is átadja a biztonsági rendszergazdának. 

![Az Azure Key Vault működésének áttekintése][1]

A fejlesztők közvetlenül is kezelhetik a kulcsokat API-k használatával. További információkért tekintse meg a [Key Vault fejlesztői útmutatóját](developers-guide.md).

## <a name="next-steps"></a>További lépések

További információ [a trezor biztonságossá tétele érdekében).](secure-your-key-vault.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).
