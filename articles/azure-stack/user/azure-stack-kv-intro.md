---
title: Az Azure Stack a Key Vault bemutatása |} A Microsoft Docs
description: Ismerje meg, hogyan kezeli az Azure Stack Key Vault a kulcsok és titkok
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: a6b4e8c3543d4681c92fbbde30eec0a543fcb0fd
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139283"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>A Key Vault az Azure Stack bemutatása

## <a name="prerequisites"></a>Előfeltételek 

* Meg kell előfizetés egy ajánlatra, amely magában foglalja az Azure Key Vault szolgáltatást.  
* [PowerShell van konfigurálva, az Azure Stackkel való használathoz](azure-stack-powershell-configure-user.md).
 
## <a name="key-vault-basics"></a>A Key Vault alapjai
Az Azure Stack a Key Vault segít a kriptográfiai kulcsokat, és a titkos kódok, amelyek felhőalapú alkalmazásokat és szolgáltatásokat használja. A Key Vault használata esetén, titkosítsa a kulcsokat és titkos kódokat, például:
   * Hitelesítési kulcsok 
   * Tárfiókkulcsok
   * Az adattitkosítási kulcsokat
   * PFX-fájlokat
   * Jelszavak

A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők a fejlesztéshez és a teszteléshez percek alatt létrehozhatják a kulcsokat, később pedig zökkenőmentesen áttelepíthetik őket éles kulcsokká. A biztonsági rendszergazdák igény szerint adhatják meg (és vonhatják vissza) a kulcsokkal kapcsolatos engedélyeket.

Az Azure Stack-előfizetés birtokában bárki létrehozhat és használhat kulcstárolót. Bár a Key Vault elsősorban a fejlesztők és a biztonsági rendszergazdák, a szervezet számára más Azure Stack-szolgáltatásokat felügyelő operátor végrehajtása, és felügyelheti azokat. Például az Azure Stack operátori bejelentkezhet az Azure Stack-előfizetéshez, hozzon létre egy tárolót a szervezet kulcsainak tárolásához és felel, a üzemeltetési feladatokhoz:

* Hozzon létre, vagy importáljon egy kulcs vagy titkos kód.
* Visszavonása, illetve egy kulcs vagy titkos kód törlése.
* Engedélyezze a felhasználók vagy alkalmazások, így azok kezeléséhez, vagy használja a kulcsok és titkos kulcsok a key vault eléréséhez.
* Kulcshasználat konfigurálása (például jelentkezzen vagy titkosítása).

Az üzemeltető majd rendelkező egységes erőforrás-azonosítók (URI) meghívhatnak fejlesztőknek biztosít. Operátorok is lehetővé teszi biztonsági rendszergazdák kulcshasználat naplózás adatokkal.

A fejlesztők közvetlenül is kezelhetik a kulcsokat API-k használatával. További információ a Key Vault fejlesztői útmutatójában talál.

## <a name="scenarios"></a>Forgatókönyvek
A következő forgatókönyveket ismertetik, hogyan segíti a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek.

### <a name="developer-for-an-azure-stack-application"></a>Fejlesztői az Azure Stack-alkalmazáshoz
**Probléma:** szeretnék írni egy alkalmazást az Azure Stack, amely kulcsokat használ az aláíráshoz és titkosításhoz. Ezek a kulcsok az alkalmazáson, lehet, így, hogy a megoldás egy földrajzilag elosztott alkalmazás szeretnék.

**Utasítás:** kulcsok tárolása tárolóban történik, és szükség esetén egy URI-t hív.

### <a name="developer-for-software-as-a-service-saas"></a>Fejlesztői szoftverfrissítési szolgáltatásként (SaaS)
**Probléma:** saját ügyfél a kulcsok és titkos kulcsok nem szeretnék felelősséget és esetleges felelőssége. Ügyfelek kezelhetik a kulcsokat, és szeretném, hogy szeretnék végezni, mi a teendő, leginkább, amely biztosítja az alapvető szoftverfunkciók összpontosíthat.

**Utasítás:** ügyfelek importálhatják a saját kulcsaikat az Azure Stack, és felügyeli őket. 

### <a name="chief-security-officer-cso"></a>Biztonsági vezető (CSO)
**Probléma:** szeretném helyezni, ellenőrizze, hogy a szervezet a kulcs életciklusához kapcsolódó felett van, és képes figyelni a kulcshasználatot.

**Utasítás:** Key Vault célja, hogy a Microsoft nem tekintheti meg és fejtheti vissza a kulcsokat. -Alkalmazásnak titkosítási műveleteket ügyfelek kulcsainak használatával kell, amikor a Key Vault használja a kulcsok az alkalmazás nevében. Az alkalmazás nem látja az ügyfelek kulcsait. Bár több Azure Stack-szolgáltatásokat és erőforrásokat, az Azure Stack egyetlen helyről kezelheti a kulcsokat. A tároló egyetlen felületet, hány tárolóval rendelkezik az Azure Stackben, mely régiókban, függetlenül biztosít, támogatás, és mely alkalmazások használják őket.

## <a name="next-steps"></a>További lépések

* [A portál használatával kezelheti a Key Vault az Azure Stackben](azure-stack-kv-manage-portal.md)  
* [A Key Vault az Azure Stackben kezelése a PowerShell használatával](azure-stack-kv-manage-powershell.md)

