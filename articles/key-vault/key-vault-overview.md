---
title: Az Azure Key Vault – áttekintés – az Azure Key Vault |} A Microsoft Docs
description: Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: c96899fc46cd9cf6377894de773785dac14f3345
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121566"
---
# <a name="what-is-azure-key-vault"></a>Mi az Azure Key Vault?

Az Azure Key Vault segít a következő problémák megoldásához:

- **Titkos kódok kezelése** – Az Azure Key Vaulttal biztonságosan tárolhatók a jogkivonatok, jelszavak, tanúsítványok, API-kulcsok és egyéb titkos kulcsok, valamint szigorúan szabályozható az ezekhez való hozzáférés
- **Kulcskezelés** – Az Azure Key Vault kulcskezelési megoldásként is használható. Az Azure Key Vaulttal egyszerűen létrehozhatja és vezérelheti az adatok titkosításához használt titkosítási kulcsokat. 
- **Tanúsítványkezelés** – Az Azure Key Vault egyben egy olyan szolgáltatás is, amellyel egyszerűen építhet ki, kezelhet és helyezhet üzembe az Azure-ral és a belső csatlakoztatott erőforrásokkal használható nyilvános és privát SSL- és TLS-tanúsítványokat. 
- **Hardveres biztonsági modullal támogatott titkos kulcsok tárolása** – A titkos kulcsok és a kulcsok védelmét szoftverek vagy a 2. szintű FIPS 140-2 szerint érvényesített hardveres biztonsági modulok (HSM-ek) is biztosíthatják

## <a name="why-use-azure-key-vault"></a>Miért érdemes az Azure Key Vaultot használni?

### <a name="centralize-application-secrets"></a>Titkos alkalmazáskulcsok központosítása

A titkos alkalmazáskulcsok tárolásának központosítása az Azure Key Vaultban lehetővé teszi azok elosztásának irányítását. A Key Vault nagymértékben csökkenti a veszélyét annak, hogy a titkos kulcsok véletlenül kiszivárogjanak. A Key Vault használatával az alkalmazásfejlesztőknek többé nem kell az alkalmazásban tárolniuk a biztonsági információkat. Nem rendelkezik biztonsági adatok tárolására az alkalmazások nem, hogy ez a szabályzat információk részének kell. Tegyük fel például, hogy egy alkalmazásnak egy adatbázishoz kell csatlakoznia. Helyett a kapcsolati karakterláncot az alkalmazáskódban lévő, Ön is tárolja biztonságos helyen a Key Vaultban.

Az alkalmazások biztonságosan férhetnek hozzá az URI-k használatával adatokat. Az URI-k lehetővé teszik az alkalmazások titkos kulcs bizonyos verziójának lekérését. Hiba esetén nem kell a Key vaultban tárolt titkos információk megvédéséhez egyéni kód írására.

### <a name="securely-store-secrets-and-keys"></a>A titkos kulcsok és a kulcsok biztonságos tárolása

A titkos kulcsok és a kulcsok számára az Azure biztosít védelmet az ipari szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulok (HSM) segítségével. A használt HSM-eket a FIPS 140-2 2. szintje szerint ellenőrizték.

A kulcstartóhoz való hozzáférés előtt a hívónak (felhasználónak vagy alkalmazásnak) megfelelő hitelesítésre és engedélyezésre van szüksége. A hitelesítés a hívó azonosítását szolgálja, az engedélyezés pedig meghatározza, milyen műveletek elvégzésére jogosult.

A hitelesítés az Azure Active Directory használatával történik. Az engedélyezés szerepköralapú hozzáférés-vezérléssel (RBAC) vagy Key Vault hozzáférési szabályzattal történhet. Az RBAC a tárolók kezeléséhez használható, a kulcstartó hozzáférési szabályzatát pedig a tárolóban található adatokhoz való hozzáféréskor használja a rendszer.

Az Azure Key Vault-kulcstartók szoftveresen vagy hardveresen lehetnek HSM-védettek. Az olyan esetekben, ahol nagyobb biztonságra van szükség, hardveres biztonsági modulokkal importálhat vagy hozhat létre a HSM határait mindig betartó kulcsokat. A Microsoft Thales hardveres biztonsági modult használ. A Thales-eszközökkel vihet át kulcsokat a HSM-ből az Azure Key Vaultba.

Végül pedig az Azure Key Vault kialakításából adódóan a Microsoft nem tekintheti meg és nem fejtheti vissza az adatokat.

### <a name="monitor-access-and-use"></a>A hozzáférés és használat monitorozása

Miután létrehozott néhány kulcstartót, monitorozhatja, hogyan és mikor férnek hozzá a kulcsokhoz és a titkos kulcsokhoz. A tárolók naplózásának engedélyezésével követheti a tevékenység. Az Azure Key Vaultot beállíthatja az alábbiak elvégzésére:

- Archiválás tárfiókba
- Streamelés eseményközpontba
- Naplók küldése a Log Analyticsnek

Engedélyekkel rendelkezik a naplók felett, és meg is védheti őket a hozzáférés korlátozásával, illetve törölheti azokat a naplókat, amelyekre már nincs szüksége.

### <a name="simplified-administration-of-application-secrets"></a>A titkos alkalmazáskulcsok egyszerűsített adminisztrációja

Értékes adatok tárolásakor el kell végeznie néhány lépést. Biztonsági információknak védettnek kell lenniük, követniük kell az életciklusuk, magas rendelkezésre állású kell lennie. Az Azure Key Vault leegyszerűsíti a folyamatot, ezeknek a követelményeknek:

- Nem lesz többé szükség a hardveres biztonsági modulok (HSM-ek) házon belüli ismeretére
- Rendkívül gyorsan igazodik a vállalata használati igényeiben jelentkező növekedésekhez.
- A kulcstartója tartalmát egy régión belül és egy másodlagos régióba replikálja. Az adatreplikáció biztosítja a magas rendelkezésre állást, és szükségtelenné a műveleteket kell rendszergazdai elindíthatja a feladatátvételt.
- Standard Azure felügyeleti lehetőségeket biztosít a Portal, az Azure CLI és a PowerShell segítségével.
- Automatizálja a nyilvános hitelesítésszolgáltatóktól vásárolt tanúsítványokon elvégzett egyes műveleteket, például a regisztrálást és a megújítást.

Továbbá az Azure Key Vault-kulcstartók lehetővé teszik a titkos alkalmazáskulcsok elkülönítését. Csak a tároló elérésére jogosult alkalmazások hozzáférhetnek, és csak a meghatározott műveletek elvégzéséhez korlátozott lehet. Alkalmazásonként egy Azure Key Vault-kulcstárolót hozhat létre, és a benne tárolt titkos kulcsok használatát csak egy adott alkalmazásra és fejlesztői csapatra korlátozhatja.

### <a name="integrate-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

Az Azure-ban biztonságos tárolóként a Key Vault rendelkezik egyszerűsítésére használják helyzetek, például:
-  [Az Azure Disk Encryption](../security/azure-security-disk-encryption.md)
-  A [mindig titkosított]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkció az SQL server és az Azure SQL Database
- [Az Azure App Service]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). 

A Key Vault integrálható tárfiókokkal, eseményközpontokkal és a Log Analyticsszel.

## <a name="next-steps"></a>További lépések

- [Gyors útmutató: Hozzon létre egy Azure Key Vault, a parancssori felületről](quick-create-cli.md)
- [Azure-webalkalmazások konfigurálása a Key Vault titkos kulcsainak olvasásához](tutorial-web-application-keyvault.md)
