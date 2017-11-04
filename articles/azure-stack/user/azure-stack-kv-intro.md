---
title: "Az Azure Key Vault verem bemutatása |} Microsoft Docs"
description: "Ismerje meg, hogyan kezeli az Azure verem Key Vault a kulcsok és titkos kulcsok"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: sngun
ms.openlocfilehash: 621a0cb865d0c050d7271d10bd14076f9f0c6f67
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Bevezetés az Azure-készletben kulcstároló

## <a name="prerequisites"></a>Előfeltételek 

* Az ajánlat, amely tartalmazza az Azure Key Vault szolgáltatás elő kell fizetnie.  
* [PowerShell Azure verem való használatra van beállítva](azure-stack-powershell-configure-user.md).
 
## <a name="key-vault-basics"></a>Key Vault alapjai
Verem Azure Key Vault segít a titkosítási kulcsok védelme és titkos kulcsok, amelyek a felhőalapú alkalmazások és szolgáltatások használatára. Key Vault használatával titkosíthatja kulcsok és titkos kulcsok, például:
   * Hitelesítési kulcsokat 
   * Tárfiókkulcsok
   * Az adattitkosítási kulcsokat
   * PFX-fájlokat
   * Jelszavak

A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. A fejlesztők a fejlesztéshez és a teszteléshez percek alatt létrehozhatják a kulcsokat, később pedig zökkenőmentesen áttelepíthetik őket éles kulcsokká. A biztonsági rendszergazdák igény szerint adhatják meg (és vonhatják vissza) a kulcsokkal kapcsolatos engedélyeket.

Egy Azure verem előfizetés birtokában bárki létrehozhat és használhat kulcstárolót. Bár a Key Vault elsősorban a fejlesztők és rendszergazdák, a szervezet más Azure-verem szolgáltatások felügyelő operátor valósítja meg, és kezelheti. Az Azure verem operátor bejelentkezhet egy Azure verem-előfizetést, például a kulcsok tárolására, és akkor lesz a működési feladatok felelős a szervezet tároló létrehozása:

* Hozzon létre, vagy importáljon a kulcsok vagy titkos kulcsok.
* Visszavonására, és a kulcs vagy titkos kulcs törlése.
* Felhasználók vagy -alkalmazásokat a kulcstároló, így azok kezeléséhez, vagy használja a benne tárolt kulcsokat és titkos kulcsok engedélyezése.
* Kulcshasználat konfigurálása (például regisztráljon vagy titkosítása).

Az operátor majd biztosíthat a fejlesztők egységes erőforrás-azonosítók (URI) meghívhatnak saját alkalmazásaikból. Operátorok kulcshasználat naplózási információk biztonsági rendszergazdák is biztosíthat.

A fejlesztők közvetlenül is kezelhetik a kulcsokat API-k használatával. További információkért lásd: a Key Vault fejlesztői útmutatója.

## <a name="scenarios"></a>Forgatókönyvek
A következő esetekben ismertetik, hogyan segíti a Key Vault a fejlesztők és a biztonsági rendszergazdák igényeinek.

### <a name="developer-for-an-azure-stack-application"></a>Az Azure-verem alkalmazás fejlesztői
**Probléma:** szeretnék írni a kérelmet az aláírási és titkosítási kulcsokat használó Azure verem. Ezek a kulcsok kívül legyenek, lehet, hogy a megoldás megfelelő-e olyan alkalmazás, amely a földrajzilag elosztott szeretnék.

**Utasítás:** kulcsok tárolása tárolóban, és szükség esetén egy URI által meghívott.

### <a name="developer-for-software-as-a-service-saas"></a>Fejlesztő a szolgáltatott szoftverként (SaaS)
**Probléma:** nem szeretnék felelősséget és esetleges felelőssége saját felhasználói kulcsok és titkos kulcsok. Az ügyfelek számára saját maguk kezelnék a kulcsaikat, így I pedig arra összpontosíthatnék Mi a teendő, legjobb, amely biztosítja a core szoftverfunkciók kívánt.

**Utasítás:** ügyfelek importálhatják a saját kulcsaikat az Azure-vermet, és kezelheti azokat. 

### <a name="chief-security-officer-cso"></a>Fő biztonsági vezető (CSO)
**Probléma:** kívánt győződjön meg arról, hogy a saját szervezetem kézben tartja a kulcs életciklusához kapcsolódó, és képes figyelni a kulcshasználatot.

**Utasítás:** Key Vault kialakításának köszönhetően a Microsoft tekintheti meg, illetve nem nyerheti ki a kulcsokat. Amikor egy alkalmazásnak titkosítási műveleteket ügyfelek kulcsainak használatával kell, Key Vault használ a kulcsok az alkalmazás nevében. Az alkalmazás nem látja az ügyfelek kulcsait. Bár több Azure verem szolgáltatásokat és erőforrásokat használjuk, a kulcsok Azure verem egyetlen helyről kezelheti. A tároló egyetlen felületet, függetlenül attól, hogy Ön hány tárolóval rendelkezik Azure-készletben, mely régiókat azok biztosít, támogatása, és mely alkalmazások használják őket.

## <a name="next-steps"></a>Következő lépések

* [A verem Azure Key Vault kezeléséhez a portál használatával](azure-stack-kv-manage-portal.md)  
* [A verem Azure Key Vault kezelése a PowerShell használatával](azure-stack-kv-manage-powershell.md)

