---
title: Partner-azonosító csatolása a partner által a delegált erőforrásokon felhasználható kreditek engedélyezéséhez
description: Megtudhatja, hogyan rendelheti hozzá partner-AZONOSÍTÓját az Azure Lighthouse használatával kezelt felhasználói erőforrásokhoz kapcsolódó partneri kreditek (PEC) fogadásához.
ms.date: 10/05/2020
ms.topic: how-to
ms.openlocfilehash: 5caa205ce74152c7ec047952f66c1bf9188ddf02
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776170"
---
# <a name="link-your-partner-id-to-enable-partner-earned-credit-on-delegated-resources"></a>Partner-azonosító csatolása a partner által a delegált erőforrásokon felhasználható kreditek engedélyezéséhez

Ha Ön a [Microsoft Partner Network](https://partner.microsoft.com/)tagja, összekapcsolhatja a Partner azonosítóját a delegált ügyfelek erőforrásainak kezeléséhez használt hitelesítő adatokkal. Így nyomon követheti az ügyfelek bevonásait és a [felügyelt szolgáltatásokra (PEC) vonatkozó partneri kreditek](/partner-center/partner-earned-credit)fogadását.

Ha az [Azure Marketplace-en felügyelt szolgáltatási ajánlatokat használó ügyfeleket](publish-managed-services-offers.md)készít, ez a hivatkozás automatikusan megtörténik, az ajánlatok közzétételéhez használt FIÓKPARTNER-azonosítóval. Ezen ügyfelek esetében nincs szükség további műveletre a PEC fogadásához.

Ha az [ügyfeleket Azure Resource Management-sablonok használatával](onboard-customer.md)készíti elő, a hivatkozás létrehozásához műveletet kell végrehajtania. Ezt úgy teheti meg, hogy az [MPN-azonosítót összekapcsolja](../../cost-management-billing/manage/link-partner-id.md) legalább egy olyan felhasználói fiókkal, amely az összes előfizetett előfizetéshez hozzáfér.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>Partner-azonosító hozzárendelése új ügyfelek bevezetéséhez

Ha Azure Resource Manager-sablonok (ARM-sablonok) használatával készíti elő az ügyfeleket, az alábbi eljárással összekapcsolhatja a partner AZONOSÍTÓját, és engedélyezheti a partner által létrehozott kreditet. A lépések elvégzéséhez ismernie kell az [MPN-Partner azonosítóját](/partner-center/partner-center-account-setup#locate-your-mpn-id) . Ügyeljen arra, hogy a partneri profilban látható **társított MPN-azonosítót** használja.

Az egyszerűség kedvéért javasoljuk, hogy hozzon létre egy egyszerű szolgáltatásnevet a bérlőben, csatolja a **társított MPN-azonosítóhoz**, majd minden ügyfél számára hozzáférést biztosítson a [PEC-re jogosult Azure beépített szerepkörhöz](https://docs.microsoft.com/partner-center/azure-roles-perms-pec).

1. [Hozzon létre egy egyszerű szolgáltatásnevet](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) a kezelő bérlőben. Ebben a példában az egyszerű szolgáltatásnév-Automation-fiókot fogjuk elnevezni.
1. Az egyszerű szolgáltatás fiókjának használatával [csatolja a társított MPN-azonosítót](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) a felügyeleti bérlőben. Ezt csak egyszer kell elvégeznie.
1. Amikor [ARM-sablonokkal](onboard-customer.md)végez bevezetést, ügyeljen arra, hogy olyan hitelesítést tartalmazzon, amely tartalmazza a PEC Automation-fiókot egy olyan [Azure beépített szerepkörrel](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)rendelkező felhasználóként, amely jogosult a PEC használatára.

A lépéseket követve minden Ön által kezelt ügyfél-bérlő társítva lesz a partner-AZONOSÍTÓhoz, amely lehetővé teszi, hogy a PEC-t fogadja az ügyfelek számára. A PEC Automation-fióknak nem kell hitelesítenie vagy végrehajtania semmilyen műveletet az ügyfél bérlője számára.

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>A partner-azonosító hozzáadása a korábban bekészített ügyfelekhez

Ha már előkészített egy ügyfelet, előfordulhat, hogy nem kíván újabb központi telepítést végrehajtani a PEC Automation-fiók egyszerű telepítéséhez. Ehelyett összekapcsolhatja a **társított MPN-azonosítót** egy olyan felhasználói fiókkal, amelynek már van hozzáférése az ügyfél bérlője munkahelyéhez. Győződjön meg arról, hogy a fiók olyan [Azure beépített szerepkört kapott, amely jogosult a PEC](https://docs.microsoft.com/partner-center/azure-roles-perms-pec)használatára.

Ha a fiók a [társított MPN-azonosítóhoz van társítva](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) a felügyeleti bérlőben, akkor az ügyfélnek a PEC-t fogja tudni fogadni.

## <a name="confirm-partner-earned-credit"></a>Partner által létrehozott kredit megerősítése

[A PEC részleteit a Azure Portalban tekintheti](/partner-center/partner-earned-credit-explanation#azure-cost-management) meg, és megerősítheti, hogy a PEC Milyen költségekkel részesült.

Ha követte a fenti lépéseket, és nem látja a társítást, nyisson meg egy támogatási kérést a Azure Portal.

## <a name="next-steps"></a>Következő lépések

- További információ a [Microsoft partner Networkról](/partner-center/mpn-overview).
- Útmutató [a PEC kiszámításához és kifizetéséhez](/partner-center/partner-earned-credit-explanation).
- Az [ügyfelek](onboard-customer.md) bevonása az Azure Lighthouse-ba.
