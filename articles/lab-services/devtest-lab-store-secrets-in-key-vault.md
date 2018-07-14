---
title: Store titkos kulcsok a key vault az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Útmutató az Azure Key vaultban titkos kulcsok tárolására, és használhassák őket egy virtuális gép, képlet, vagy egy környezet létrehozása közben.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 0397c520dd0135df56e7eb7e8cd6ed7ffa46156e
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009391"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Store titkos kulcsok a key vault az Azure DevTest Labs szolgáltatásban
Előfordulhat, hogy meg kell adnia egy összetett titkos kulcs Azure DevTest Labs használata esetén: Windows virtuális gép Linux rendszerű virtuális gép vagy a személyes hozzáférési tokent egy összetevő révén a Git-tárház klónozásához a nyilvános SSH-kulcs jelszava. Titkos kódok általában hosszúak és véletlenszerű karakter hosszúságú lehet. Ezért őket lehet megkülönböztetni és kényelmetlen lehet, különösen akkor, ha több alkalommal használja a titkos kulcsot.

A probléma megoldásához, és ne a titkos kulcsok egy biztonságos helyre, DevTest Labs tárolását titkok támogatja egy [az Azure key vault](../key-vault/key-vault-overview.md). A felhasználó első alkalommal tárolja a titkos kulcs, a DevTest Labs szolgáltatás automatikusan létrehoz egy kulcstárolót ugyanazt az erőforráscsoportot, amely tartalmazza a tesztkörnyezetben, és tárolja a titkos kulcsot a kulcstartóban. 

## <a name="save-a-secret-in-azure-key-vault"></a>Az Azure Key Vaultban titkos kulcs mentése
Szeretné menteni a titkos kulcsot az Azure Key Vaultban, tegye a következőket:

1. Válassza ki **Moje tajné kódy** a bal oldali menüben.
2. Adjon meg egy **neve** számára a titkos kulcsot. Ezt a nevet a virtuális gép létrehozásakor, képlet, a legördülő listából vagy egy környezet megjelenik. 
3. Adja meg a titkos kulcsot, mint a **érték**.

    ![Store titkos kulcs](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Azure Key vault titkos kulcs használata
Adja meg egy titkos kulcsot, hozzon létre egy virtuális Gépet, képlet, vagy egy környezetre van szüksége, ha manuálisan adja meg a titkos kulcs, vagy ki egy mentett titkos kulcsot a kulcstartóban található. A key vaultban tárolt titkos kulcs használatára, tegye a következőket:

1. Válassza ki **mentett titkos kulcs használata**. 
2. A legördülő listából válassza ki a a titkos kód **titkos kód kiválasztása**. 

    ![A virtuális gép titkos kulcs használata](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Titkos kulcs használata az Azure Resource Manager-sablon
A titkos kód nevét adhatja meg az Azure Resource Manager-sablon, amellyel a virtuális gép létrehozása az alábbi példában látható módon:

![Használja a titkos képletet vagy környezetben](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>További lépések

- [A titkos kulcs virtuális gép létrehozása](devtest-lab-add-vm.md) 
- [A titkos kulcs használatával képlet létrehozása](devtest-lab-manage-formulas.md)
- [A titkos kulcs-környezet létrehozása](devtest-lab-create-environment-from-arm.md)
