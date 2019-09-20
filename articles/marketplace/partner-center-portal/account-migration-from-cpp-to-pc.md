---
title: Fiók áttelepítése Cloud Partner Portalról a partneri központba – kereskedelmi piactér az Azure-hoz
description: Fiók áttelepítésének módja a CPP-ből a partneri központba. -Kereskedelmi piactér az Azure-hoz
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/09/2019
ms.openlocfilehash: 13cbd799a1ffb877ace2231bfb854764edac9c90
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147121"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Fiók áttelepítése Cloud Partner Portalról a partneri központba

Ha rendelkezik meglévő CPP-fiókkal, a fiók beállításait át kell telepíteni a partner központba.

## <a name="account-migration-process"></a>Fiók áttelepítési folyamata

Ha egy adott fiókhoz tartozó tulajdonosi szerepkörrel rendelkező felhasználó, a közzétevő profil lapján megjelenik egy sárga szalagcím. A következő két eset egyikéhez tartozhat:

- A fiókja már át lett telepítve, és készen áll a Fiókbeállítások felügyeletére a partner Centerben.
- További műveleteket kell elvégeznie a fiók áttelepítésének befejezéséhez a partner központba.

### <a name="your-account-has-been-migrated-to-partner-center"></a>A fiókja át lett telepítve a partner központba

Minden olyan fiók esetében, amely a CPP-ből a partneri központba való áttelepítést végezte, a fiókkezelés a partner Centerben fog történni. A módosításokat, például a felhasználók hozzáadását/törlését a rendszer szinkronizálja a CPP-vel.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Még nem telepítette át a fiókját a partner központba

A fiók áttelepítési folyamatának elindításához kattintson a szalagcímre. A következő elemeket kell megadnia:

1. Munkahelyi e-mail-cím: <br> <br> A legtöbb esetben jelentkezzen be a CPP-be való bejelentkezéshez használt e-mail-címmel. Bizonyos esetekben egy másik e-mail-címet kell használni:

    * Microsoft-fiók: Ha a CPP-fiók egy Microsoft-fiók, akkor meg kell adnia egy érvényes munkahelyi e-mail-címet, amely ahhoz a bérlőhöz tartozik, amelyhez az MPN-azonosító regisztrálva van.

    * Nem egyező bérlő: Ha a munkahelyi e-mail-címe nem tartozik a CPP-fiókjában található Microsoft Partner Network-AZONOSÍTÓhoz társított bérlőhöz, hibaüzenet jelenik meg. Ha ezt a hibát szeretné áthelyezni, adjon meg egy, a bérlőhöz társított e-mail-címet. A megjelenő hibaüzenet megadja a bérlő nevét.

2. Regisztráció Microsoft Partner Network programra

    Abban az esetben, ha a CPP-fiók nem rendelkezik Microsoft Partner Network-AZONOSÍTÓval, vagy ha az egyik érvénytelen, akkor az aktiválási folyamat részeként regisztrálnia kell a Microsoft Partner Network programra.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Regisztráció Microsoft Partner Network programra

Azoknak a vállalatoknak, akik partneri kapcsolatot szeretnének a Microsofttal, csatlakoztassa a Microsoft Partner Network (MPN) Ha már tagja a Microsoft Partner Networknek, és van egy MPN-azonosítója, ügyeljen rá, hogy a fiók aktiválási folyamata során meg kell adnia.  

Ha még nem tagja a Microsoft Partner Networknak, [csatlakozhat most](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) egy MPN-azonosító beszerzéséhez. Ügyeljen arra, hogy jegyezze fel az MPN-azonosítót, mert a fiók aktiválási folyamata során meg kell adnia.

További információ a Microsoft Partner Networkről: [Csatlakozás a Microsoft Partner Network](https://partner.microsoft.com/en-US/membership) a partner webhelyén. Ha többet szeretne megtudni arról, hogy miként csatlakozhat a Microsoft Partner Network a ISV-hoz, tekintse meg az [ISV erőforrás-központ](https://partner.microsoft.com/isv-resource-hub)című témakört.  

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Dynamics 365-alapú megoldások áthelyezése a partneri központba

Ha az egyik kereskedelmi partner GTM-portálon a Dynamics 365 for Customer engagement vagy a Dynamics 365 for Finance and Operations megoldásokat hozta létre, akkor **ezeket a megoldásokat most a partner Centerben kell felügyelni**.

**Ha a megoldásokat nem a 2019-es augusztus 31-ig helyezte át, akkor**a lehető leghamarabb hajtsa végre az alábbi lépéseket. Ehhez a következőket kell tennie:

- A független szoftvergyártók nem férhetnek hozzá a marketing előnyeihez
- A közös értékesítés prioritása elveszti az állapotukat
- A felhőalapú beágyazást igénylők a 2019. október 15. után nem felelnek meg az előírásoknak

> [!NOTE]
> Ha az MPN-tagsági fiók eredetileg a Partner tagsági központban (PMC) lett létrehozva, jelentkezzen be a [partner központba](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , és győződjön meg róla, hogy a fiókja át lett telepítve az alábbi lépések végrehajtása előtt. Ha a profil képernyőjét az MPN-AZONOSÍTÓval látja, készen áll a folytatásra. Ha nem, el kell indítania a fiók áttelepítését a [Partner tagsági központban](https://partners.microsoft.com/partnerprogram/Welcome.aspx)megjelenő utasításokat követve. Ha segítségre van szüksége ezzel kapcsolatban, látogasson el a [támogatási szolgálatba](https://partner.microsoft.com/support?issueid=100-0077).

1. Lépjen a [kereskedelmi piactér – áttekintés lapra a partner Centerben](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Ha a bal oldali navigációs panelen a "kereskedelmi piactér" felirat látható, akkor a rendszer regisztrálja, és továbblép a következő lépésre. Ha nem, [Regisztráljon most a kereskedelmi piactéren](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .

2. Az ajánlatait [megkeresve](https://appsource.microsoft.com/)erősítse meg ajánlatait a AppSource. Ha az ajánlatok már a AppSource-ben vannak, folytassa a következő lépéssel. A AppSource-ben nem szereplő bármely ajánlathoz hozzon létre egy [új dynamics 365-et a Customer engagement ajánlathoz](create-new-customer-engagement-offer.md) vagy egy [új Dynamics 365 for Operations ajánlatot](create-new-operations-offer.md).

3. Ellenőrizze a regisztrációját a Business Applications ISV-összekötő programban:
   * A partner Center [szerződések](https://partner.microsoft.com/dashboard/account/agreements) lapján ellenőrizze, hogy elfogadta-e a **Business Applications ISV-kiegészítést** a programban való regisztráláshoz.
   * A [Fiókbeállítások](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) lapon adja meg a számlázási adatokat.

4. Minden új és meglévő minősítési ajánlat beküldése, még akkor is, ha az ajánlatokat korábban már minősítették. Ha jogosult, kérheti, hogy részt vegyen a prémium szintű csomagban a folyamat során. Ha az ajánlatát korábban tanúsították, **akkor a 2019. október 15-én végre kell hajtania az alkalmazások újraminősítését.** A minősítés vagy az újraminősítés megköveteli, hogy az alkalmazás támogassa a Business Applications platform legújabb verzióját.

5. Lépjen az [egyik kereskedelmi partner GTM-portálra](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) , és adja hozzá a AppSource-lista URL-címét a Marketplace-hivatkozások szakaszban. Ha segítségre van szüksége ehhez a lépéshez, küldjön e cosell@microsoft.com-mailt a következő címre:.

## <a name="next-steps"></a>További lépések

- [A kereskedelmi piactér-fiók kezelése a partner Centerben](./manage-account.md) 
