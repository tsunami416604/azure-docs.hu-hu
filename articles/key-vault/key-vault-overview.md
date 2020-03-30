---
title: Az Azure Key Vault áttekintése – Azure Key Vault | Microsoft dokumentumok
description: Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4e2953b107b017d032e737e2878472166c677839
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78194954"
---
# <a name="what-is-azure-key-vault"></a>Mi az Azure Key Vault?

Az Azure Key Vault a következő problémák megoldásában nyújt segítséget:

- **Titkos kódok kezelése** – Az Azure Key Vaulttal biztonságosan tárolhatók a jogkivonatok, jelszavak, tanúsítványok, API-kulcsok és egyéb titkos kulcsok, valamint szigorúan szabályozható az ezekhez való hozzáférés
- **Kulcskezelés** – Az Azure Key Vault kulcskezelési megoldásként is használható. Az Azure Key Vaulttal egyszerűen létrehozhatja és vezérelheti az adatok titkosításához használt titkosítási kulcsokat. 
- **Tanúsítványkezelés** – Az Azure Key Vault egy olyan szolgáltatás is, amely lehetővé teszi a nyilvános és privát átviteli réteg biztonsági/secure sockets layer (TLS/SSL) tanúsítványok azure-ral és a belső csatlakoztatott erőforrásokkal való egyszerű kiépítését, kezelését és üzembe helyezését. 
- **Hardveres biztonsági modulok által támogatott titkok tárolása** - A titkok és kulcsok szoftverrel vagy FIPS 140-2 Level 2 érvényesített HSM-ekkel védhetők

## <a name="why-use-azure-key-vault"></a>Miért érdemes az Azure Key Vaultot használni?

### <a name="centralize-application-secrets"></a>Titkos alkalmazáskulcsok központosítása

A titkos alkalmazáskulcsok tárolásának központosítása az Azure Key Vaultban lehetővé teszi azok elosztásának irányítását. A Key Vault nagymértékben csökkenti a veszélyét annak, hogy a titkos kulcsok véletlenül kiszivárogjanak. A Key Vault használatával az alkalmazásfejlesztőknek többé nem kell az alkalmazásban tárolniuk a biztonsági információkat. Ha nem kell biztonsági információkat tárolni az alkalmazásokban, akkor nincs szükség arra, hogy ez az információ a kód részévé legyen. Tegyük fel például, hogy egy alkalmazásnak egy adatbázishoz kell csatlakoznia. Ahelyett, hogy a kapcsolati karakterláncot az alkalmazás kódjában tárolnák, biztonságosan tárolhatja azt a Key Vaultban.

Az alkalmazások biztonságosan hozzáférhetnek a szükséges információkhoz az URI-k használatával. Ezek az URI-k lehetővé teszik, hogy az alkalmazások egy titkos titok adott verzióit lekérhetik. Nincs szükség egyéni kód írására a Key Vaultban tárolt titkos adatok védelméhez.

### <a name="securely-store-secrets-and-keys"></a>A titkos kulcsok és a kulcsok biztonságos tárolása

A titkos kulcsok és a kulcsok számára az Azure biztosít védelmet az ipari szabványoknak megfelelő algoritmusok, kulcshosszok és hardveres biztonsági modulok (HSM) segítségével. A használt HSM-eket a FIPS 140-2 2. szintje szerint ellenőrizték.

A kulcstartóhoz való hozzáférés előtt a hívónak (felhasználónak vagy alkalmazásnak) megfelelő hitelesítésre és engedélyezésre van szüksége. A hitelesítés a hívó azonosítását szolgálja, az engedélyezés pedig meghatározza, milyen műveletek elvégzésére jogosult.

A hitelesítés az Azure Active Directory használatával történik. Az engedélyezés szerepköralapú hozzáférés-vezérléssel (RBAC) vagy Key Vault hozzáférési szabályzattal történhet. Az RBAC a tárolók kezeléséhez használható, a kulcstartó hozzáférési szabályzatát pedig a tárolóban található adatokhoz való hozzáféréskor használja a rendszer.

Az Azure Key Vault-kulcstartók szoftveresen vagy hardveresen lehetnek HSM-védettek. Az olyan esetekben, ahol nagyobb biztonságra van szükség, hardveres biztonsági modulokkal importálhat vagy hozhat létre a HSM határait mindig betartó kulcsokat. A Microsoft nCipher hardveres biztonsági modulokat használ. Az nCipher eszközök segítségével áthelyezhet egy kulcsot a HSM-ből az Azure Key Vaultba.

Végül pedig az Azure Key Vault kialakításából adódóan a Microsoft nem tekintheti meg és nem fejtheti vissza az adatokat.

### <a name="monitor-access-and-use"></a>A hozzáférés és használat monitorozása

Miután létrehozott néhány kulcstartót, monitorozhatja, hogyan és mikor férnek hozzá a kulcsokhoz és a titkos kulcsokhoz. A tárolók naplózásának engedélyezésével figyelheti a tevékenységeket. Az Azure Key Vaultot beállíthatja az alábbiak elvégzésére:

- Archiválás tárfiókba
- Streamelés eseményközpontba
- Küldje el a naplókat az Azure Monitor naplóiba.

Engedélyekkel rendelkezik a naplók felett, és meg is védheti őket a hozzáférés korlátozásával, illetve törölheti azokat a naplókat, amelyekre már nincs szüksége.

### <a name="simplified-administration-of-application-secrets"></a>A titkos alkalmazáskulcsok egyszerűsített adminisztrációja

Értékes adatok tárolásakor el kell végeznie néhány lépést. A biztonsági információkat biztosítani kell, életciklust kell követniük, és magas rendelkezésre állásúnak kell lenniük. Az Azure Key Vault leegyszerűsíti az alábbi követelmények teljesítésének folyamatát:

- A hardveres biztonsági modulok házon belüli ismeretének szükségességének megszüntetése.
- Rendkívül gyorsan igazodik a vállalata használati igényeiben jelentkező növekedésekhez.
- A kulcstartója tartalmát egy régión belül és egy másodlagos régióba replikálja. Az adatreplikáció biztosítja a magas rendelkezésre állást, és elveszi a rendszergazda minden műveletének szükségességét a feladatátvétel elindításához.
- Standard Azure felügyeleti lehetőségeket biztosít a Portal, az Azure CLI és a PowerShell segítségével.
- Automatizálja a nyilvános hitelesítésszolgáltatóktól vásárolt tanúsítványokon elvégzett egyes műveleteket, például a regisztrálást és a megújítást.

Továbbá az Azure Key Vault-kulcstartók lehetővé teszik a titkos alkalmazáskulcsok elkülönítését. Az alkalmazások csak a tárolóhoz férhetnek hozzá, amelyhez hozzáférhetnek, és csak meghatározott műveletek végrehajtására korlátozhatók. Alkalmazásonként egy Azure Key Vault-kulcstárolót hozhat létre, és a benne tárolt titkos kulcsok használatát csak egy adott alkalmazásra és fejlesztői csapatra korlátozhatja.

### <a name="integrate-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

Az Azure biztonságos tárolójaként a Key Vault olyan forgatókönyvek egyszerűsítésére szolgál, mint például:
-  [Azure Disk Encryption](../security/fundamentals/encryption-overview.md)
-  Az SQL server és az Azure SQL Database [mindig titkosított]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkciói
- [Az Azure App Service szolgáltatás.]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate) 

A Key Vault integrálható tárfiókokkal, eseményközpontokkal és a naplóelemzéssel.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Azure Key Vault-kulcstartó létrehozása a parancssori felülettel](quick-create-cli.md)
- [Azure-webalkalmazás konfigurálása titkos kulcs olvasásához a Key Vaultból](tutorial-web-application-keyvault.md)
