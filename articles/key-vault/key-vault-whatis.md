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
ms.topic: get-started-article
ms.date: 08/02/2018
ms.author: barclayn
ms.openlocfilehash: 08331a399044eba17060d15f24af1863df38caf5
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480253"
---
# <a name="what-is-azure-key-vault"></a>Mi az Azure Key Vault?

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Key Vault lehetővé teszi, hogy hardveres biztonsági modulokkal (HSM) védett kulcsokkal titkosítsa a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiókok kulcsait, az adattitkosítási kulcsokat, a PFX-fájlokat és a jelszavakat). A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban. Ha ezt teszi, a Microsoft a FIPS 140-2 2. szintje szerint ellenőrzött hardveres biztonsági modulokban (hardverek és belső vezérlőprogramok) dolgozza fel a kulcsokat.  

A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők a fejlesztéshez és a teszteléshez percek alatt létrehozhatják a kulcsokat, később pedig zökkenőmentesen áttelepíthetik őket éles kulcsokká. A biztonsági rendszergazdák igény szerint adhatják meg (és vonhatják vissza) a kulcsokkal kapcsolatos engedélyeket.

## <a name="basic-concepts"></a>Alapfogalmak

Az Azure Key Vault egy titkos kulcsok biztonságos tárolására és hozzáférésére használható eszköz. Titkos kulcsnak számít minden olyan adat, amelynek a hozzáféréstét szigorúan korlátozni kívánja, például: API-kulcsok, jelszavak vagy tanúsítványok.
Az alábbiakban néhány kulcsfontosságú fogalom ismertetése található:
- **Bérlő** – A bérlő az a szervezet, amely egy Microsoft-felhőszolgáltatás egy adott példányát birtokolja és kezeli. A kifejezéssel általában egy szervezet Azure- és Office 365-szolgáltatásainak pontos listájára hivatkozunk.
- **Kulcstartó-tulajdonos** – Létrehozhat egy Key Vault-tárolót, amely felett teljes körű hozzáféréssel és irányítással rendelkezik. Emellett beállíthat naplózást, amellyel naplózhatja a titkos kulcsok elérését. A kulcsok életciklusát a rendszergazdák kezelhetik. Kiadhatnak új kulcsverziókat, biztonsági másolatokat készíthetnek stb.
- **Erőforrás** – Egy olyan kezelhető elem, amely az Azure-on keresztül érhető el. Általános erőforrás például a következő: virtuális gép, tárfiók, webalkalmazás, adatbázis, virtuális hálózat, de számos további fajtája is létezik.
- **Erőforráscsoport** – Egy olyan tároló, amely egy Azure-megoldáshoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Lásd az erőforráscsoportokat.
- **Kulcstartóhasználó** – Műveleteket hajthat végre a Key Vault-tárolóban található objektumokon, ha a kulcstartó-tulajdonos felruházta hozzáféréssel. Ez a kiosztott jogosultságokon múlik.
- Az **[Azure Active Directory](../active-directory/active-directory-whatis.md)** egy adott bérlő Azure AD-szolgáltatása. Minden címtárhoz tartozik egy vagy több tartomány. Egy címtárhoz számos előfizetés tartozhat, de csak egyetlen bérlő. 
- **Azure-bérlő azonosítója** – Egy egyedi módszer az Azure-előfizetéseken belüli Azure Active Directory-címtárak azonosítására. 
- **Felügyeltszolgáltatás-identitás** – Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. A felügyeltszolgáltatás-identitás (MSI) segít leegyszerűsíteni ezt a problémát, mivel az Azure-szolgáltatások számára egy automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD-ben). Ezzel az identitással anélkül végezhet hitelesítést a Key Vaultban vagy bármely, Azure AD-hitelesítést támogató szolgáltatásban, hogy a hitelesítő adatokat a kódban kellene tárolnia. Az MSI-ről további információkat [itt olvashat](../active-directory/managed-service-identity/overview.md).

    ![Az MSI ábrája](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Key Vault-szerepkörök

Az alábbi táblázat segítségével jobban megértheti, hogyan segíti a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek kielégítését.

| Szerepkör | Probléma leírása | Az Azure Key Vault megoldása |
| --- | --- | --- |
| Azure-alkalmazásfejlesztő |„Olyan alkalmazást szeretnék írni az Azure rendszerre, amely kulcsokat használ az aláíráshoz és a titkosításhoz, viszont azt szeretném, hogy ezek a kulcsok az alkalmazáson kívül legyenek, hogy a megoldás egy földrajzilag elosztott alkalmazáshoz is használható legyen. <br/><br/>Emellett azt szeretném, hogy ezek a kulcsok és titkos kulcsok el legyenek látva védelemmel, anélkül, hogy meg kellene írnom a kódot. Azt is szeretném, hogy a kulcsokat és a titkos kulcsokat könnyedén használni lehessen az alkalmazásaimból, optimális teljesítmény mellett.” |√ A kulcsok tárolása tárolóban történik, és szükség esetén egy URI segítségével lehet előhívni őket.<br/><br/> √ A kulcsok számára az Azure biztosít védelmet az ipari szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulok (HSM) segítségével.<br/><br/> √ A kulcsok feldolgozása hardveres biztonsági modulokban történik, amelyek ugyanazokban az Azure-adatközpontokban találhatók, mint az alkalmazások. Ez nagyobb megbízhatóságot és kisebb késést eredményez, mintha a kulcsok egy külön helyen, például a helyszínen lennének. |
| SaaS-fejlesztő |„Nem szeretnék felelősséget és esetleges felelősségre vonást vállalni az ügyfeleim bérlőinek kulcsaiért és titkos kulcsaiért. <br/><br/>Szeretném, ha az ügyfeleim saját maguk kezelnék a kulcsaikat, én pedig arra összpontosíthatnék, amihez leginkább érték: az alapvető szoftverfunkciók biztosítására.” |√ Az ügyfelek importálhatják a saját kulcsaikat az Azure rendszerbe, és kezelhetik őket. Amikor egy SaaS-alkalmazásnak titkosítási műveleteket kell végrehajtania az ügyfelek kulcsaival, a Key Vault ezeket a műveleteket az alkalmazás nevében végzi. Az alkalmazás nem látja az ügyfelek kulcsait. |
| Biztonsági vezető (CSO) |„Szeretnék biztos lenni abban, hogy az alkalmazásaink a biztonságos kulcskezelés tekintetében megfelelnek a FIPS 140-2 2. szintje szerint ellenőrzött HSM-eknek. <br/><br/>Biztos szeretnék lenni abban is, hogy a szervezetünk kézben tartja a kulcsok életciklusát, valamint meg tudja figyelni a kulcshasználatot. <br/><br/>Továbbá – bár több Azure-szolgáltatást és -erőforrást is használunk – egyetlen helyről szeretném kezelni a kulcsokat az Azure rendszerén belül.” |√ A hardveres biztonsági modulokat a FIPS 140-2 2. szintje szerint ellenőrizték.<br/><br/>√ A Key Vault kialakításának köszönhetően a Microsoft nem tekintheti meg, illetve nem nyerheti ki a kulcsokat.<br/><br/>√ Közel valós idejű kulcshasználat-naplózás.<br/><br/>√ A tároló egyetlen felületet biztosít függetlenül attól, hogy Ön hány tárolóval rendelkezik az Azure rendszerben, ezek mely régiókat támogatják, illetve mely alkalmazások használják őket. |

Azure-előfizetés birtokában bárki létrehozhat és használhat kulcstárolót. Bár a Key Vault elsősorban a fejlesztők és a rendszergazdák számára hasznos, a szervezet más Azure-szolgáltatásait kezelő rendszergazdák is beállíthatják és kezelhetik. Ez a rendszergazda például bejelentkezhet egy Azure-előfizetéssel, létrehozhat egy tárolót a szervezet kulcsainak tárolásához, majd elvégezheti az olyan üzemeltetési feladatokat, mint például:

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

A Key Vault használatának naplózásával kapcsolatos további információkért tekintse meg [Az Azure Key Vault naplózása](key-vault-logging.md) című cikket.

A kulcsoknak és titkos kulcsoknak az Azure Key Vault szolgáltatással való használatával kapcsolatos további információkért tekintse meg az [About Keys, Secrets, and Certificates](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx) (Információk a kulcsokról, titkos kulcsokról és tanúsítványokról) című cikket.

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).
