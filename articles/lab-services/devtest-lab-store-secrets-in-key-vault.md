---
title: Titkok tárolása az Azure DevTest Labs kulcstartójában | Microsoft dokumentumok
description: Megtudhatja, hogyan tárolhatja a titkos kulcsokat egy Azure Key Vaultban, és használja őket virtuális gép, képlet vagy környezet létrehozása közben.
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
ms.openlocfilehash: 0ca36a7081aaf70ee2045ee7586184c89591df16
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461514"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Titkok tárolása az Azure DevTest Labs kulcstartójában
Előfordulhat, hogy meg kell adnia egy összetett titkos azure DevTest Labs: jelszó a Windows virtuális gép, nyilvános SSH-kulcs a Linux virtuális gép, vagy a személyes hozzáférési jogkivonata a Git-tármű-tárãmete egy műterméken keresztül klónozásához. A titkok általában hosszúak és véletlenszerű karakterek. Ezért a belépésük trükkös és kényelmetlen lehet, különösen akkor, ha ugyanazt a titkot többször használja.

A probléma megoldásához és a titkos kulcsok biztonságos helyen való tárolásához a DevTest Labs támogatja a titkos kulcsok tárolását egy [Azure-beli kulcstartóban.](../key-vault/general/overview.md) Amikor egy felhasználó először ment egy titkos kulcsot, a DevTest Labs szolgáltatás automatikusan létrehoz egy key vault ugyanabban az erőforráscsoportban, amely tartalmazza a labort, és tárolja a titkos kulcsot a key vaultban. DevTest Labs létrehoz egy külön key vault minden felhasználó számára. 

Vegye figyelembe, hogy a labor felhasználó először létre kell hoznia egy tesztkörnyezet virtuális gép, mielőtt létrehozhat egy titkos kulcsot a key vaultban. Ennek az az oka, hogy a DevTest Lab szolgáltatásnak hozzá kell rendelnie a tesztkörnyezet-felhasználót egy érvényes felhasználói dokumentumhoz, mielőtt titkos kulcsokat hozhatlétre és tárolhatna a key vaultban. 


## <a name="save-a-secret-in-azure-key-vault"></a>Titkos kulcs mentése az Azure Key Vaultban
A titkos kulcs azure Key Vaultban való mentéséhez tegye a következő lépéseket:

1. Válassza a Bal oldali menü **Saját titkok** lehetőséget.
2. Adja meg a titkos név **nevét.** Ez a név jelenik meg a legördülő listában, amikor virtuális gép, képlet vagy környezet létrehozásakor. 
3. Adja meg a titkos kulcsot **értékként.**

    ![Üzlet titka](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Az Azure Key Vault titkos adatainak használata
Ha meg kell adnia egy titkos kulcsot egy virtuális gép, képlet vagy környezet létrehozásához, manuálisan megadhat egy titkos kulcsot, vagy kiválaszthat egy mentett titkos kulcsot a key vaultból. A kulcstartóban tárolt titkos fájlok használatához tegye a következő műveleteket:

1. Válassza **a Mentett titkos kulcsot használja**lehetőséget. 
2. Válassza ki a titkát a Legördülő listából a **Válasszon egy titkot.** 

    ![Titkos használat a virtuális gépben](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Titkos titok használata az Azure Resource Manager-sablonban
Megadhatja a titkos nevét egy Azure Resource Manager-sablonban, amely a virtuális gép létrehozásához használatos, ahogy az a következő példában látható:

![Titkos kulcsot használhat a képletben vagy a környezetben](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>További lépések

- [Virtuális gép létrehozása a titkos ítmény használatával](devtest-lab-add-vm.md) 
- [Képlet létrehozása a titkos értékkel](devtest-lab-manage-formulas.md)
- [Környezet létrehozása a titkos ítinadt használva](devtest-lab-create-environment-from-arm.md)
