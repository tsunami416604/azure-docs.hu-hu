---
title: "Szolgáltatásidentitás (MSI) az Azure Active Directory felügyelete"
description: "Az Azure-erőforrások Szolgáltatásidentitás felügyelt áttekintése."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: 914d09a73026356c836a6eb468818cc43664ec2e
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Szolgáltatás-identitás (MSI) felügyelt Azure-erőforrások

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Egy közös kérdés esetén felhőalapú alkalmazások kezelése a hitelesítő adatokat, amelyek kell lenniük a kódban való hitelesítéséhez. Fontos feladat biztosíthatja a rendszer ezen hitelesítő adatok védelmét. Ideális esetben azok soha nem jelennek meg a fejlesztői munkaállomásokon, vagy beolvasása beadja a verziókövetési rendszerrel. Az Azure Key Vault biztonságosan tárolni a hitelesítő adatokat és egyéb kulcsok és titkos lehetőséget biztosít, de a kódot kell hitelesítenie magát a Key Vault kérheti le azokat. Felügyelt szolgáltatás identitásának (MSI) teszi egyszerűbbé válik a probléma megoldásához adjon az Azure-szolgáltatások automatikusan felügyelt identitást az Azure Active Directory (Azure AD). Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, többek között a Key Vault, anélkül, hogy a hitelesítő adatok a kódban a szolgáltatással való hitelesítésre szolgáló használhatja.

## <a name="how-does-it-work"></a>Hogyan működik?

Amikor felügyelt Szolgáltatásidentitás engedélyezi az Azure-szolgáltatások, Azure automatikusan létrehozza a szolgáltatáspéldány az identitás használják az Azure-előfizetéshez az Azure AD-bérlő.  A színfalak Azure látja el, a szolgáltatáspéldány az identitás hitelesítő adatait.  A kódot is végezze el a helyi kérelmek hozzáférési jogkivonatok lekérésére, szolgáltatások, amelyek támogatják az Azure AD-alapú hitelesítés.  Azure intézkedik működés közbeni szolgáltatáspéldány használt hitelesítő adatokat.  A szolgáltatáspéldány törlődik, ha Azure automatikusan törli azokat a hitelesítő adatok és az identitás Azure AD-ben.

Íme egy példa, felügyelt Szolgáltatásidentitás Azure virtuális gépek működését.

![Virtuális gép MSI – példa](./media/msi-vm-example.png)

1. Az Azure Resource Manager ahhoz, hogy a virtuális gép MSI üzenetet kap.
2. Az Azure Resource Manager az Azure AD-határoz meg a virtuális gép identitásának hoz létre egy egyszerű szolgáltatást. A szolgáltatás egyszerű az előfizetés által megbízhatónak minősített Azure AD-bérlő jön létre.
3. Az Azure Resource Manager szolgáltatás egyszerű részleteit azt állítja be az MSI-fájl a virtuális gép Virtuálisgép-bővítmény.  Ez a lépés ügyfél-azonosító és a hozzáférési jogkivonatok lekérni az Azure AD a bővítmény által használt tanúsítvány konfigurálását foglalja magában.
4. Most, hogy a szolgáltatás egyszerű a virtuális gép ismerik fel, akkor engedélyezhetők Azure-erőforrások eléréséhez.  Például ha a kódot kell hívni az Azure Resource Manager, majd rendelne a virtuális gép szolgáltatás egyszerű szerepköralapú hozzáférés-vezérlést (RBAC) használata az Azure ad-ben a megfelelő szerepkörrel.  Ha a kódot kell hívni a Key Vault, majd meg volna hozzáférést a kódot az adott titkos kód vagy a kulcsot a Key Vault.
5. A virtuális gépen a kód egy token kér a helyi végpont az MSI-Virtuálisgép-bővítmény által futtatott: http://localhost:50342/oauth2/jogkivonat.  Az erőforrás-paraméter, amelyhez a tokent küldött a szolgáltatás. Például, ha azt szeretné, hogy a kód az Azure Resource Manager hitelesítéséhez, használhatja erőforrás = https://management.azure.com/.
6. Az MSI Virtuálisgép-bővítmény olyan hozzáférési jogkivonatot kérhet az Azure AD a beállított ügyfél-azonosító és a tanúsítványt használja.  Az Azure AD egy JSON webes jogkivonat (JWT) hozzáférési jogkivonatot ad vissza.
7. A kód elküldi a hozzáférési jogkivonat egy olyan szolgáltatás, amely támogatja az Azure AD authentication hívásakor.

Minden Azure szolgáltatás, amely támogatja a felügyelt Szolgáltatásidentitás a saját metódusa egy hozzáférési jogkivonat beszerzése a kódot. Tekintse meg az egyes szolgáltatásokhoz, hogy megtudja, az adott metódus használatával kérje le a jogkivonatot az oktatóanyagok.

## <a name="try-managed-service-identity"></a>Próbálja meg a felügyelt identitás

Tekintse meg a felügyelt Szolgáltatásidentitás oktatóanyag megtudhatja végpontok-forgatókönyvek különböző Azure-erőforrások eléréséhez:
<br><br>
| Az MSI-kompatibilis erőforrás | A webinárium témái: |
| ------- | -------- |
| Azure virtuális gép (Windows) | [Access Azure Data Lake Store és egy Windows virtuális gép felügyelt identitás](msi-tutorial-windows-vm-access-datalake.md) |
|                    | [Hozzáférés az Azure erőforrás-kezelő és egy Windows virtuális gép felügyelt identitás](msi-tutorial-windows-vm-access-arm.md) |
|                    | [Hozzáférés az Azure SQL és egy Windows virtuális gép felügyelt identitás](msi-tutorial-windows-vm-access-sql.md) |
|                    | [Access Azure Storage keresztül a hozzáférési kulcsot a Windows virtuális gép által felügyelt-szolgáltatási identitás](msi-tutorial-windows-vm-access-storage.md) |
|                    | [Hozzáférés az Azure tárolási és egy Windows virtuális gép SAS keresztül felügyelt identitás](msi-tutorial-windows-vm-access-storage-sas.md) |
|                    | [A Windows virtuális gép felügyelt Szolgáltatásidentitás és az Azure Key Vault nem Azure AD erőforrás eléréséhez](msi-tutorial-windows-vm-access-nonaad.md) |
| Azure virtuális gép (Linux)   | [Az Azure Data Lake Store Linux virtuális gép felügyelt identitás](msi-tutorial-linux-vm-access-datalake.md) |
|                    | [Hozzáférés az Azure erőforrás-kezelő Linux virtuális gép felügyelt identitás](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Access Azure Storage segítségével a hozzáférési kulcsot a Linux virtuális gép felügyelt Szolgáltatásidentitás](msi-tutorial-linux-vm-access-storage.md) |
|                    | [Hozzáférés az Azure tárolási Linux virtuális gép SAS keresztül felügyelt identitás](msi-tutorial-linux-vm-access-storage-sas.md) |
|                    | [A Linux virtuális gép felügyelt Szolgáltatásidentitás és az Azure Key Vault nem Azure AD erőforrás eléréséhez](msi-tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Az Azure App Service vagy az Azure Functions felügyelt identitás használatára](/azure/app-service/app-service-managed-service-identity) |
| Azure Function     | [Az Azure App Service vagy az Azure Functions felügyelt identitás használatára](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Felhasználói Szolgáltatásidentitást az Azure Service Bus által felügyelt](../service-bus-messaging/service-bus-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Mely Azure-szolgáltatások által felügyelt szolgáltatás identitás támogatásának?

Azure-szolgáltatásokat, amely támogatja a felügyelt Szolgáltatásidentitás MSI használhatja az Azure AD-alapú hitelesítés támogató szolgáltatások felé történő hitelesítésre.  Végezzük MSI és az Azure AD hitelesítési integrálása az Azure között.  Ellenőrizze gyakran frissítéseit.

### <a name="azure-services-that-support-managed-service-identity"></a>Azure-szolgáltatásokat, amely támogatja a felügyelt identitás

Az Azure-szolgáltatásokat támogatja a felügyelt szolgáltatás identitást.

| Szolgáltatás | status | Dátum | Konfigurálás | A jogkivonat beolvasása |
| ------- | ------ | ---- | --------- | ----------- |
| Azure-alapú virtuális gépek | Előzetes verzió | 2017. szeptember | [Azure Portal](msi-qs-configure-portal-windows-vm.md)<br>[PowerShell](msi-qs-configure-powershell-windows-vm.md)<br>[Azure CLI](msi-qs-configure-cli-windows-vm.md)<br>[Az Azure Resource Manager-sablonok](msi-qs-configure-template-windows-vm.md) | [REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http)<br>[.NET](msi-how-to-use-vm-msi-token.md#get-a-token-using-c)<br>[Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[Go](msi-how-to-use-vm-msi-token.md#get-a-token-using-go)<br>[PowerShell](msi-how-to-use-vm-msi-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Előzetes verzió | 2017. szeptember | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager-sablon](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | Előzetes verzió | 2017. szeptember | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager-sablon](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Előzetes verzió | 2017. november | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-szolgáltatások, a támogatás az Azure AD-hitelesítés

A következő szolgáltatásokat támogatja az Azure AD-alapú hitelesítés, és felügyelt Szolgáltatásidentitás használó ügyfél szolgáltatással lettek tesztelve.

| Szolgáltatás | Erőforrás-azonosító | status | Dátum | Hozzáférés hozzárendelése |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Elérhető | 2017. szeptember | [Azure Portal](msi-howto-assign-access-portal.md) <br>[PowerShell](msi-howto-assign-access-powershell.md) <br>[Azure CLI](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net/ | Elérhető | 2017. szeptember | |
| Azure Data Lake | https://datalake.azure.net/ | Elérhető | 2017. szeptember | |
| Azure SQL | https://database.windows.net/ | Elérhető | 2017. október | |
| Azure Event Hubs | https://eventhubs.azure.net/ | Elérhető | 2017. december | |
| Azure Service Bus | https://servicebus.azure.net/ | Elérhető | 2017. december | |

## <a name="how-much-does-managed-service-identity-cost"></a>Milyen mértékű nem felügyelt Szolgáltatásidentitás költség?

Felügyelt Szolgáltatásidentitás tartalmaz Azure Active Directory ingyenes, az Azure-előfizetések az alapértelmezett.  További költség nélkül felügyelt-identitás van.

## <a name="support-and-feedback"></a>Támogatás és visszajelzés

Szívesen meghallgatnánk a véleményét!

* Kérdései vannak az útmutató a Stack Overflow a címkével ellátott [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Kérelmek szolgáltatás, vagy visszajelzés a [a fejlesztők számára az Azure AD-visszajelzési fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






