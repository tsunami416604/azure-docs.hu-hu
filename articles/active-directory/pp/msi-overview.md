---
title: "Szolgáltatásidentitás (MSI) az Azure Active Directory felügyelete"
description: "Az Azure-erőforrások Szolgáltatásidentitás felügyelt áttekintése."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 53577c8da5f82235284d1cb9e48f2d47254aa6bd
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Szolgáltatás-identitás (MSI) felügyelt Azure-erőforrások

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Egy közös kérdés esetén felhőalapú alkalmazások kezelése a hitelesítő adatokat, amelyek kell lenniük a kódban való hitelesítéséhez. Fontos feladat biztosíthatja a rendszer ezen hitelesítő adatok védelmét. Ideális esetben azok soha nem jelennek meg a fejlesztői munkaállomásokon, vagy beolvasása beadja a verziókövetési rendszerrel. Az Azure Key Vault biztonságosan tárolni a hitelesítő adatokat és egyéb kulcsok és titkos lehetőséget biztosít, de a kódot kell hitelesítenie magát a Key Vault kérheti le azokat. Felügyelt szolgáltatás identitásának (MSI) teszi egyszerűbbé válik a probléma megoldásához adjon az Azure-szolgáltatások automatikusan felügyelt identitást az Azure Active Directory (Azure AD). Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, többek között a Key Vault, anélkül, hogy a hitelesítő adatok a kódban a szolgáltatással való hitelesítésre szolgáló használhatja.

## <a name="how-does-it-work"></a>Hogyan működik?

Egy Azure szolgáltatáspéldány felügyelt Szolgáltatásidentitás használatakor Azure identitást használja az Azure-előfizetéshez az Azure AD-bérlő hoz létre. Emellett Azure látja el az identitás, a szolgáltatáspéldány, a hitelesítő adatai. Ennek eredményeképpen a kódot is majd kérelmet helyi hozzáférési jogkivonatok lekérésére, amelyek támogatják az Azure AD hitelesítési szolgáltatásokat. Minden, amíg az Azure gondoskodik működés közbeni szolgáltatáspéldány használt hitelesítő adatokat.

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>Hogyan engedélyezhető az erőforrások felügyelt szolgáltatás identitás használatára?

Nincsenek elérhető szolgáltatás-identitások felügyelt kétféle: *rendszer adott* és *felhasználó hozzárendelt*.

- A **rendszer adott** MSI közvetlenül az Azure service-példány engedélyezve van. Az engedélyezési folyamat Azure hozza létre a szolgáltatáspéldány az identitás, az Azure AD-bérlő és látja el, a szolgáltatáspéldány az identitás hitelesítő adatait. A hozzárendelt MSI közvetlenül kapcsolódik az Azure rendszer életciklusának szolgáltatáspéldány engedélyezve van a. A szolgáltatáspéldány törlődik, ha Azure automatikusan törli azokat a hitelesítő adatok és az identitás Azure AD-ben.

- A **felhasználó hozzárendelt** MSI *(magán előnézetben)* különálló Azure erőforráscsoport létrejön. A létrehozási folyamat Azure identitást az Azure AD-bérlő hoz létre. Az identitás a létrehozása után egy vagy több Azure-szolgáltatások példányokhoz rendelhető. Mivel a felhasználó által hozzárendelt MSI segítségével több Azure szolgáltatáspéldány, életciklusa külön kezelnek.

Íme egy példa egy alapértelmezett MSI működése Azure virtuális gépekkel.

![Virtuális gép MSI – példa](~/articles/active-directory/media/msi-vm-example.png)

1. Az Azure Resource Manager ahhoz, hogy a virtuális gép alapértelmezett MSI üzenetet kap.
2. Az Azure Resource Manager az Azure AD-határoz meg a virtuális gép identitásának hoz létre egy egyszerű szolgáltatást. A szolgáltatás egyszerű az előfizetés által megbízhatónak minősített Azure AD-bérlő jön létre.
3. Az Azure Resource Manager szolgáltatás egyszerű részleteit azt állítja be az MSI-fájl a virtuális gép Virtuálisgép-bővítmény. Ez a lépés ügyfél-azonosító és a hozzáférési jogkivonatok lekérni az Azure AD a bővítmény által használt tanúsítvány konfigurálását foglalja magában.
4. Most, hogy a szolgáltatás egyszerű a virtuális gép ismerik fel, akkor engedélyezhetők Azure-erőforrások eléréséhez. Például ha a kódot kell hívni az Azure Resource Manager, majd rendelne a virtuális gép szolgáltatás egyszerű szerepköralapú hozzáférés-vezérlést (RBAC) használata az Azure ad-ben a megfelelő szerepkörrel.  Ha a kódot kell hívni a Key Vault, majd meg volna hozzáférést a kódot az adott titkos kód vagy a kulcsot a Key Vault.
5. A virtuális gépen a kód egy token kér a helyi végpont az MSI-Virtuálisgép-bővítmény által futtatott: http://localhost:50342/oauth2/jogkivonat. Az erőforrás-paraméter, amelyhez a tokent küldött a szolgáltatás. Például, ha azt szeretné, hogy a kód az Azure Resource Manager hitelesítéséhez, használhatja erőforrás = https://management.azure.com/.
6. Az MSI Virtuálisgép-bővítmény olyan hozzáférési jogkivonatot kérhet az Azure AD a beállított ügyfél-azonosító és a tanúsítványt használja.  Az Azure AD egy JSON webes jogkivonat (JWT) hozzáférési jogkivonatot ad vissza.
7. A kód elküldi a hozzáférési jogkivonat egy olyan szolgáltatás, amely támogatja az Azure AD authentication hívásakor.

Az azonos ábra, itt egy példa egy felhasználó által hozzárendelt MSI működéséről az Azure virtuális gépek használatával.

![Virtuális gép MSI – példa](~/articles/active-directory/media/msi-vm-example.png)

1. Az Azure Resource Manager kap egy üzenetet a felhasználó által hozzárendelt MSI-fájl létrehozásához.
2. Az Azure Resource Manager az Azure AD az MSI identitásának képviselő hoz létre egy egyszerű szolgáltatást. A szolgáltatás egyszerű az előfizetés által megbízhatónak minősített Azure AD-bérlő jön létre.
3. Az Azure Resource Manager konfigurálhatja az egyszerű szolgáltatás részletei a virtuális gépek MSI Virtuálisgép-bővítmény üzenetet kap. Ez a lépés ügyfél-azonosító és a hozzáférési jogkivonatok lekérni az Azure AD a bővítmény által használt tanúsítvány konfigurálását foglalja magában.
4. Most, hogy a szolgáltatás egyszerű MSI ismerik fel, akkor engedélyezhetők Azure-erőforrások eléréséhez. Például ha a kódot kell hívni az Azure Resource Manager, majd rendelne MSI szolgáltatás egyszerű szerepköralapú hozzáférés-vezérlést (RBAC) használata az Azure ad-ben a megfelelő szerepkörrel. Ha a kódot kell hívni a Key Vault, majd meg volna hozzáférést a kódot az adott titkos kód vagy a kulcsot a Key Vault. Megjegyzés: 3. lépés nincs szükség a teljes 4. Ha létezik egy olyan MSI Csomaghoz, azt is hozzáférést erőforrásokhoz, függetlenül a virtuális gép-e konfigurálva.
5. A virtuális gépen a kód egy token kér a helyi végpont az MSI-Virtuálisgép-bővítmény által futtatott: http://localhost:50342/oauth2/jogkivonat. Az ügyfél-azonosító paraméter neve az MSI-identitás használatára. Továbbá az erőforrás paraméter, amelyhez a tokent küldött a szolgáltatás. Például, ha azt szeretné, hogy a kód az Azure Resource Manager hitelesítéséhez, használhatja erőforrás = https://management.azure.com/.
6. Az MSI Virtuálisgép-bővítmény ellenőrzi, ha a kért ügyfél-azonosító tanúsítványa van konfigurálva, és egy hozzáférési jogkivonatot az Azure AD-kérelmek. Az Azure AD egy JSON webes jogkivonat (JWT) hozzáférési jogkivonatot ad vissza.
7. A kód elküldi a hozzáférési jogkivonat egy olyan szolgáltatás, amely támogatja az Azure AD authentication hívásakor.

Minden Azure szolgáltatás, amely támogatja a felügyelt Szolgáltatásidentitás a saját metódusa egy hozzáférési jogkivonat beszerzése a kódot. Tekintse meg az egyes szolgáltatásokhoz, hogy megtudja, az adott metódus használatával kérje le a jogkivonatot az oktatóanyagok.

## <a name="try-managed-service-identity"></a>Próbálja meg a felügyelt identitás

Tekintse meg a felügyelt Szolgáltatásidentitás oktatóanyag megtudhatja végpontok-forgatókönyvek különböző Azure-erőforrások eléréséhez:
<br><br>
| Az MSI-kompatibilis erőforrás | A webinárium témái: |
| ------- | -------- |
| Azure virtuális gép (Linux)   | [Hozzáférés az Azure erőforrás-kezelő Linux virtuális gép felügyelt identitás](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Access Azure Storage segítségével a hozzáférési kulcsot a Linux virtuális gép felügyelt Szolgáltatásidentitás](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Mely Azure-szolgáltatások által felügyelt szolgáltatás identitás támogatásának?

Azure-szolgáltatásokat, amely támogatja a felügyelt Szolgáltatásidentitás MSI használhatja az Azure AD-alapú hitelesítés támogató szolgáltatások felé történő hitelesítésre.  Végezzük MSI és az Azure AD hitelesítési integrálása az Azure között.  Ellenőrizze gyakran frissítéseit.

### <a name="azure-services-that-support-managed-service-identity"></a>Azure-szolgáltatásokat, amely támogatja a felügyelt identitás

Az Azure-szolgáltatásokat támogatja a felügyelt szolgáltatás identitást.

| Szolgáltatás | status | Dátum | Konfigurálás | A jogkivonat beolvasása |
| ------- | ------ | ---- | --------- | ----------- |
| Azure-alapú virtuális gépek | Előzetes verzió | 2017. szeptember | [Azure CLI](msi-qs-configure-cli-windows-vm.md)<br>[Az Azure Resource Manager-sablonok](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[A HTTP/REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-szolgáltatások, a támogatás az Azure AD-hitelesítés

A következő szolgáltatásokat támogatja az Azure AD-alapú hitelesítés, és felügyelt Szolgáltatásidentitás használó ügyfél szolgáltatással lettek tesztelve.

| Szolgáltatás | Erőforrás-azonosító | status | Dátum | Hozzáférés hozzárendelése |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://Management.Azure.com/ | Elérhető | 2017. szeptember | [Azure CLI](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.Azure.NET/ | Elérhető | 2017. szeptember | |
| Azure Data Lake | https://datalake.Azure.NET/ | Elérhető | 2017. szeptember | |
| Azure SQL | https://Database.Windows.NET/ | Elérhető | 2017. október | |

## <a name="how-much-does-managed-service-identity-cost"></a>Milyen mértékű nem felügyelt Szolgáltatásidentitás költség?

Felügyelt Szolgáltatásidentitás tartalmaz Azure Active Directory ingyenes, az Azure-előfizetések az alapértelmezett.  További költség nélkül felügyelt-identitás van.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Szívesen meghallgatnánk a véleményét!

* Kérdései vannak az útmutató a Stack Overflow a címkével ellátott [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Kérelmek szolgáltatás, vagy visszajelzés a [a fejlesztők számára az Azure AD-visszajelzési fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






