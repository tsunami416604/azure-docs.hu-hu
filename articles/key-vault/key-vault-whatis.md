---
title: Mi az Azure Key Vault? | Microsoft Docs
description: Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az Azure Key Vault használatával az ügyfelek hardveres biztonsági modulokkal (HSM) védett kulcsokkal titkosíthatják a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiókok kulcsait, az adattitkosítási kulcsokat, a PFX-fájlokat és a jelszavakat).
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: barclayn
ms.openlocfilehash: 7e98853b5b2ccc779dca970337fc44217977c8c9
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342565"
---
# <a name="what-is-azure-key-vault"></a>Mi az Azure Key Vault?

Az Azure Key Vault segít megoldani az alábbi problémákat
- **Titkok kezelése** – Azure Key Vault segítségével biztonságosan tárolhatja, és szorosan jogkivonatok, jelszavak, tanúsítványok, API-kulcsok és egyéb titkok való hozzáférésének
- **Kulcskezelés** – Azure Key Vault Key Management megoldás is használható. Az Azure Key Vaulttal egyszerűen létrehozhatja és vezérelheti az adatok titkosításához használt titkosítási kulcsokat. 
- **Tanúsítványkezelés az** – Azure Key Vault szolgáltatás is, amellyel könnyen üzembe helyezése, kezelése és üzembe helyezése az Azure és a belső csatlakozó használt nyilvános és privát Secure Sockets Layer/Transport Layer Security (SSL/TLS) tanúsítványok az erőforrásokat. 
- **A titkos kulcsokat hardveres biztonsági modulok által támogatott Store** -kulcsok és titkok védelme biztosítható szoftver vagy a FIPS 140-2 2. szintje érvényesíti a HSM-EK

## <a name="basic-concepts"></a>Alapfogalmak

Az Azure Key Vault egy titkos kulcsok biztonságos tárolására és hozzáférésére használható eszköz. Titkos kulcsnak számít minden olyan adat, amelynek a hozzáféréstét szigorúan korlátozni kívánja, például: API-kulcsok, jelszavak vagy tanúsítványok. A **tároló** titkok logikai csoportja. Most már bármilyen Key Vault-műveletek végrehajtásához először azt hitelesítésre. 

Alapvetően módon 3 hitelesítéséhez a Key Vaulthoz

1. **Használatával [felügyelt identitások az Azure-erőforrások](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)**  (**javasolt és ajánlott eljárás**): Ha telepít egy alkalmazást az Azure-beli virtuális gépen, hozzárendelheti egy identitást a virtuális gép a Kulcstartóhoz, amely hozzáfér. Más felsorolt azure-erőforrásokhoz is hozzárendelhetők identitások [Itt](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview). Ezzel a módszerrel előnye az alkalmazás / szolgáltatás nem kezel elforgatási szögét az első titkos kulcsot. Az Azure automatikusan elforgatja az identitást. 
2. **Egyszerű szolgáltatás és a tanúsítvány használatával:** a 2. lehetőség egy egyszerű szolgáltatást, és a egy társított tanúsítvány, amely hozzáfér a Key Vault használatára. Az alkalmazás tulajdonosa vagy a fejlesztői ellátásának elforgatása a tanúsítványt, és ezért ez nem ajánlott
3. **Egyszerű szolgáltatás és a titkos kulcs használata:** a 3. lehetőség (a nem előnyben részesített beállítás), hogy egy egyszerű szolgáltatást, és a egy titkos kulcsot használják a hitelesítéshez a Key Vaulthoz

Néhány kulcsfontosságú fogalom:
- **Bérlő**: A bérlő az a cég vagy intézmény, amely egy Microsoft-felhőszolgáltatás egy adott példányát birtokolja és kezeli. A kifejezéssel általában egy cég vagy intézmény Azure- és Office 365-szolgáltatásainak pontos listájára hivatkozunk.
- **Kulcstartó-tulajdonos**: Létrehozhat egy Key Vaultot, amely felett teljes körű hozzáféréssel és irányítással rendelkezik. Emellett naplózást is beállíthat, amellyel naplózhatja a titkos kulcsok és a kulcsok elérését. A kulcsok életciklusát a rendszergazdák kezelhetik. Kiadhatnak új kulcsverziókat, biztonsági másolatokat készíthetnek, és elvégezhetik a kapcsolódó feladatokat.
- **Kulcstartóhasználó**: A kulcstartóhasználó műveleteket hajthat végre a Key Vaultban található objektumokon, ha a kulcstartó-tulajdonos felruházta hozzáféréssel. Az elérhető műveletek a kiosztott jogosultságoktól függnek.
- **Erőforrás**: Az erőforrás egy olyan kezelhető elem, amely az Azure-on keresztül érhető el. Általános erőforrás például a következő: virtuális gép, tárfiók, webalkalmazás, adatbázis, virtuális hálózat, de számos további fajtája is létezik.
- **Erőforráscsoport**: Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A cég számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között.
- **Egyszerű szolgáltatás** – ahhoz, hogy hozzáférhessen az Azure AD-bérlő által védett erőforrások, az entitást, amely megköveteli a hozzáférést egy rendszerbiztonsági tag jelen kell lennie. Ez igaz mind a felhasználók (a felhasználó rendszerbiztonsági tag), és az alkalmazások (egyszerű szolgáltatásnevének). A rendszerbiztonsági tagot az adott bérlőn belüli határozza meg a hozzáférési szabályzat és a felhasználó vagy alkalmazás engedélyeit. Ez a alapvető funkcióit, például a felhasználó vagy alkalmazás hitelesítése során be- és erőforrás-hozzáférés során engedélyezési lehetővé teszi.
- **[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)**: Az Azure AD egy bérlő Active Directory-szolgáltatása. Minden címtárhoz tartozik egy vagy több tartomány. Egy címtárhoz számos előfizetés tartozhat, de csak egyetlen bérlő. 
- **Azure-bérlő azonosítója**: A bérlőazonosító egy egyedi módszer az Azure AD-példány azonosítására az Azure-előfizetésen belül.
- **Felügyelt identitások az Azure-erőforrások**: Azure Key Vault lehetővé teszi a biztonságos tárolása a hitelesítő adatokat, más kulcsok és titkos, de kérheti le azokat a Key Vault hitelesítenie kell a kódot. Egyszerűbb a probléma megoldására azzal, hogy az Azure-szolgáltatások automatikusan felügyelt identitás az Azure ad-ben egy felügyelt identitás használata révén. Ezzel az identitással anélkül végezhet hitelesítést a Key Vaultban vagy bármely, Azure AD-hitelesítést támogató szolgáltatásban, hogy a hitelesítő adatokat a kódban kellene tárolnia. További információkért lásd az alábbi képen a [felügyelt identitások Azure-erőforrások áttekintő](../active-directory/managed-identities-azure-resources/overview.md).

    ![A diagram bemutatja, hogyan felügyelt identitások az Azure-erőforrások működése](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Key Vault-szerepkörök

Az alábbi táblázat segítségével jobban megértheti, hogyan segíti a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek kielégítését.

| Szerepkör | Probléma leírása | Az Azure Key Vault megoldása |
| --- | --- | --- |
| Azure-alkalmazásfejlesztő |„Olyan alkalmazást szeretnék írni az Azure rendszerre, amely kulcsokat használ az aláíráshoz és a titkosításhoz, viszont azt szeretném, hogy ezek a kulcsok az alkalmazáson kívül legyenek, hogy a megoldás egy földrajzilag elosztott alkalmazáshoz is használható legyen. <br/><br/>Azt szeretném, hogy ezek a kulcsok és titkos kulcsok el legyenek látva védelemmel, anélkül, hogy meg kellene írnom a kódot. Azt is szeretném, hogy a kulcsokat és a titkos kulcsokat könnyedén használni lehessen az alkalmazásaimból, optimális teljesítmény mellett.” |√ A kulcsok tárolása tárolóban történik, és szükség esetén egy URI segítségével lehet előhívni őket.<br/><br/> √ A kulcsok számára az Azure biztosít védelmet az ipari szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulok segítségével.<br/><br/> √ A kulcsok feldolgozása hardveres biztonsági modulokban történik, amelyek ugyanazokban az Azure-adatközpontokban találhatók, mint az alkalmazások. Ez a módszer nagyobb megbízhatóságot és kisebb késést eredményez ahhoz képest, mintha a kulcsok egy külön helyen, például a helyszínen lennének. |
| Szolgáltatott szoftverek fejlesztője (SaaS-fejlesztő) |„Nem szeretnék felelősséget és esetleges felelősségre vonást vállalni az ügyfeleim bérlőinek kulcsaiért és titkos kulcsaiért. <br/><br/>Szeretném, ha az ügyfeleim saját maguk kezelnék a kulcsaikat, én pedig arra összpontosíthatnék, amihez leginkább értek: az alapvető szoftverfunkciók biztosítására.” |√ Az ügyfelek importálhatják a saját kulcsaikat az Azure rendszerbe, és kezelhetik őket. Amikor egy SaaS-alkalmazásnak titkosítási műveleteket kell végrehajtania az ügyfelek kulcsaival, a Key Vault ezeket a műveleteket az alkalmazás nevében végzi. Az alkalmazás nem látja az ügyfelek kulcsait. |
| Biztonsági vezető (CSO) |„Szeretnék biztos lenni abban, hogy az alkalmazásaink a biztonságos kulcskezelés tekintetében megfelelnek a FIPS 140-2 2. szintje szerint ellenőrzött HSM-eknek. <br/><br/>Biztos szeretnék lenni abban is, hogy a cégünk kézben tartja a kulcsok életciklusát, valamint meg tudja figyelni a kulcshasználatot. <br/><br/>Továbbá – bár több Azure-szolgáltatást és -erőforrást is használunk – egyetlen helyről szeretném kezelni a kulcsokat az Azure rendszerén belül.” |√ A hardveres biztonsági modulokat a FIPS 140-2 2. szintje szerint ellenőrizték.<br/><br/>√ A Key Vault kialakításának köszönhetően a Microsoft nem tekintheti meg, illetve nem nyerheti ki a kulcsokat.<br/><br/>√ A kulcshasználatot közel valós időben naplózza rendszer.<br/><br/>√ A tároló egyetlen felületet biztosít függetlenül attól, hogy Ön hány tárolóval rendelkezik az Azure rendszerben, ezek mely régiókat támogatják, illetve mely alkalmazások használják őket. |

Azure-előfizetés birtokában bárki létrehozhat és használhat kulcstárolót. Bár a Key Vault elsősorban a fejlesztők és a rendszergazdák számára hasznos, a cég más Azure-szolgáltatásait kezelő rendszergazdák is beállíthatják és kezelhetik. Ez a rendszergazda például bejelentkezhet egy Azure-előfizetéssel, létrehozhat egy tárolót a cég kulcsainak tárolásához, majd elvégezhet az alábbiakhoz hasonló üzemeltetési feladatokat:

* A kulcsok vagy titkos kulcsok létrehozása és importálása
* A kulcsok vagy titkos kulcsok visszavonása, illetve törlése
* A felhasználók vagy alkalmazások feljogosítása a kulcstárolóhoz való hozzáférésre, hogy azután kezelhessék és használhassák a benne tárolt kulcsokat és titkos kulcsokat
* A kulcshasználat konfigurálása (például aláíráshoz vagy titkosításhoz)
* A kulcshasználat figyelése

A rendszergazda ezután a fejlesztők számára olyan URI-kat biztosíthat, amelyeket meghívhatnak saját alkalmazásaikból, a biztonsági rendszergazda számára pedig kulcshasználati naplózási információkkal szolgálhat. 

![Az Azure Key Vault áttekintése][1]

A fejlesztők közvetlenül is kezelhetik a kulcsokat API-k használatával. További információkért tekintse meg a [Key Vault fejlesztői útmutatóját](key-vault-developers-guide.md).

## <a name="next-steps"></a>További lépések

A rendszergazdáknak szóló bevezető oktatóanyagért tekintse meg a [Bevezetés az Azure Key Vault használatába](key-vault-get-started.md) című cikket.

A kulcstartó használatának naplózásával kapcsolatos további információkért tekintse meg [Az Azure Key Vault naplózása](key-vault-logging.md) című cikket.

A kulcsoknak és titkos kulcsoknak az Azure Key Vaulttal való használatával kapcsolatos további információkért tekintse meg [A kulcsok, titkos kódok és tanúsítványok ismertetése](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx) című cikket.

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).
