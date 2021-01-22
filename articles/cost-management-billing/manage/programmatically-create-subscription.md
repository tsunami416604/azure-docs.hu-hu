---
title: Azure-előfizetések létrehozása programozott módon
description: Ennek a cikknek a segítségével megismerheti, milyen lehetőségei vannak Azure-előfizetések programozott módon való létrehozására.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a51a158700df52ced5288cf2c2f82ebcdf3de54d
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254021"
---
# <a name="create-azure-subscriptions-programatically"></a>Azure-előfizetések létrehozása programozott módon

Ennek a cikknek a segítségével megismerheti, milyen lehetőségei vannak Azure-előfizetések programozott módon való létrehozására.

Különböző REST API-k használatával az alábbi Azure-szerződéstípusokhoz hozhat létre előfizetést:

- Nagyvállalati Szerződés (EA)
- Microsoft-ügyfélszerződés (Microsoft Customer Agreement, MCA)
- Microsoft-partnerszerződés (MPA)

A REST API-kkal nem lehet programozott módon további előfizetéseket létrehozni más szerződéstípusokhoz.

Az előfizetések létrehozásához szükséges követelmények és részletek eltérnek a különféle szerződések és API-verziók esetében. Lásd az alábbi, adott helyzetre vonatkozó cikkeket:

Legutóbbi API-k:

- [EA-előfizetések létrehozása](programmatically-create-subscription-enterprise-agreement.md)
- [MCA-előfizetések létrehozása](programmatically-create-subscription-microsoft-customer-agreement.md)
- [MPA-előfizetések létrehozása](programmatically-create-subscription-microsoft-partner-agreement.md)

Ha még [előzetes verziójú API-kat](programmatically-create-subscription-preview.md) használ, továbbra is létrehozhat velük előfizetéseket. 

Ezenkívül [ARM-sablonnal is létrehozhat előfizetéseket](create-subscription-template.md). Az ARM-sablonok segítségével REST API-kkal automatizálható az előfizetés-létrehozási folyamat. 

## <a name="next-steps"></a>Következő lépések

* Előfizetés létrehozása után elérhetővé teheti ezt a képességet más felhasználók és szolgáltatásnevek számára is. További információ: [Hozzáférés biztosítása nagyvállalati Azure-előfizetés létrehozáshoz (előzetes verzió)](grant-access-to-create-subscription.md).
* Több előfizetés felügyeleti csoportok használatával történő kezeléséről az [erőforrások Azure-beli felügyeleti csoportokkal való rendszerezését](../../governance/management-groups/overview.md) ismertető részben talál további információt.
