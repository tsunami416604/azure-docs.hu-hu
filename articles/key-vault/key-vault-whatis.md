<properties
    pageTitle="Mi az Azure Key Vault? | Microsoft Azure"
    description="Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Az Azure Key Vault használatával az ügyfelek hardveres biztonsági modulokkal (HSM) védett kulcsokkal titkosíthatják a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiókok kulcsait, az adattitkosítási kulcsokat, a PFX-fájlokat és a jelszavakat)."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/15/2016"
    ms.author="cabailey"/>




# Mi az Azure Key Vault?

Az Azure Key Vault a legtöbb régióban elérhető. További információ: [A Key Vault díjszabása](https://azure.microsoft.com/pricing/details/key-vault/).

## Bevezetés

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Key Vault lehetővé teszi, hogy hardveres biztonsági modulokkal (HSM) védett kulcsokkal titkosítsa a kulcsokat és a titkos kulcsokat (például a hitelesítési kulcsokat, a tárfiókok kulcsait, az adattitkosítási kulcsokat, a PFX-fájlokat és a jelszavakat). A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban. Ha ezt a lehetőséget választja, a Microsoft a kulcsokat a FIPS 140-2 2. szintje szerint (hardveresen és a firmware szempontjából) ellenőrzött hardveres biztonsági modulokon dolgozza fel.  

A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők a fejlesztéshez és a teszteléshez percek alatt létrehozhatják a kulcsokat, később pedig zökkenőmentesen áttelepíthetik őket éles kulcsokká. A biztonsági rendszergazdák igény szerint adhatják meg (és vonhatják vissza) a kulcsokkal kapcsolatos engedélyeket.

Az alábbi táblázat segítségével jobban megértheti, hogyan segíti a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek kielégítését.





| Szerepkör        | Probléma leírása           | Az Azure Key Vault megoldása  |
| ------------- |-------------|-----|
| Azure-alkalmazásfejlesztő      | „Olyan alkalmazást szeretnék írni az Azure rendszerre, amely kulcsokat használ az aláíráshoz és a titkosításhoz, azonban azt szeretném, hogy ezek az alkalmazásomon kívül legyenek, hogy a megoldás a földrajzilag elosztott alkalmazásokhoz is használható legyen. <br/><br/>A kulcsok és a titkos kulcsok számára védelmet is szeretnék biztosítani, ám ehhez a kódot nem szeretném magam megírni. Továbbá azt is szeretném, hogy egyszerűen, optimális teljesítmény mellett fel lehessen őket használni az alkalmazásaimból.” | √ A kulcsok tárolása tárolóban történik, és szükség esetén egy URI segítségével lehet előhívni őket.<br/><br/> √ A kulcsok számára az Azure biztosít védelmet az ipari szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulok (HSM) segítségével.<br/><br/> √ A kulcsokat olyan hardveres biztonsági modulok dolgozzák fel, amelyek ugyanazokban az Azure-adatközpontokban helyezkednek el, mint az alkalmazások, ez pedig nagyobb megbízhatóságot és kisebb késleltetést biztosít, mint a kulcsok külön, például a helyszínen történő tárolása.|
| SaaS-fejlesztő      |„Nem szeretnék felelősséget és esetleges felelősségre vonást vállalni az ügyfeleim bérlőinek kulcsaiért és titkos kulcsaiért. <br/><br/>Szeretném, ha az ügyfeleim saját maguk kezelnék a kulcsaikat, én pedig arra összpontosíthatnék, amihez leginkább érték: az alapvető szoftverfunkciók biztosítására.” | √ Az ügyfelek importálhatják a saját kulcsaikat az Azure rendszerbe, és kezelhetik őket. Amikor egy SaaS-alkalmazásnak titkosítási műveleteket kell végeznie az ügyfelek kulcsainak használatával, akkor ezt a Key Vault végzi el az alkalmazás nevében. Az alkalmazás nem látja az ügyfelek kulcsait.|
| Biztonsági vezető (CSO) | „Szeretnék biztos lenni abban, hogy az alkalmazásaink a biztonságos kulcskezelés tekintetében megfelelnek a FIPS 140-2 2. szintje szerint ellenőrzött HSM-eknek. <br/><br/>Biztos szeretnék lenni abban is, hogy a szervezetünk kézben tartja a kulcsok életciklusát, valamint meg tudja figyelni a kulcshasználatot. <br/><br/>Továbbá – bár több Azure-szolgáltatást és -erőforrást is használunk – egyetlen helyről szeretném kezelni a kulcsokat az Azure rendszerén belül.”     |√ A hardveres biztonsági modulokat a FIPS 140-2 2. szintje szerint ellenőrizték.<br/><br/>√ A Key Vault kialakításának köszönhetően a Microsoft nem tekintheti meg, illetve nem nyerheti ki a kulcsokat.<br/><br/>√ Közel valós idejű kulcshasználat-naplózás.<br/><br/>√ A tároló egyetlen felületet biztosít függetlenül attól, hogy Ön hány tárolóval rendelkezik az Azure rendszerben, ezek mely régiókat támogatják, illetve mely alkalmazások használják őket. |


Azure-előfizetés birtokában bárki létrehozhat és használhat kulcstárolót. Bár a Key Vault elsősorban a fejlesztők és a rendszergazdák számára hasznos, a szervezet más Azure-szolgáltatásait kezelő rendszergazdák is beállíthatják és kezelhetik. Ez a rendszergazda például bejelentkezhet egy Azure-előfizetéssel, létrehozhat egy tárolót a szervezet kulcsainak tárolásához, majd elvégezheti az olyan üzemeltetési feladatokat, mint például:

+ A kulcsok vagy titkos kulcsok létrehozása és importálása
+ A kulcsok vagy titkos kulcsok visszavonása, illetve törlése
+ A felhasználók vagy alkalmazások feljogosítása a kulcstárolóhoz való hozzáférésre, hogy azután kezelhessék és használhassák a benne tárolt kulcsokat és titkos kulcsokat
+ A kulcshasználat konfigurálása (például aláíráshoz vagy titkosításhoz)
+ A kulcshasználat figyelése

A rendszergazda ezután a fejlesztők számára olyan URI-kat biztosíthat, amelyeket meghívhatnak saját alkalmazásaikból, a biztonsági rendszergazda számára pedig kulcshasználati naplózási információkkal szolgálhat. 

   ![Az Azure Key Vault áttekintése][1]

A fejlesztők közvetlenül is kezelhetik a kulcsokat API-k használatával. További információkért tekintse meg a [Key Vault fejlesztői útmutatóját](key-vault-developers-guide.md).

## Következő lépések

A rendszergazdáknak szóló bevezető oktatóanyagért tekintse meg a [Bevezetés az Azure Key Vault használatába](key-vault-get-started.md) című cikket.

A Key Vault használatának naplózásával kapcsolatos további információkért tekintse meg [Az Azure Key Vault naplózása](key-vault-logging.md) című cikket.

A kulcsok és a titkos kulcsok Azure Key Vault segítségével történő használatával kapcsolatos további információkért tekintse meg az [About Keys and Secrets](https://msdn.microsoft.com/library/azure/dn903623.aspx) (Kulcsok és titkos kulcsok) című cikket.


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png



<!--HONumber=Sep16_HO4-->


