---
title: Fiók áttelepítése Cloud Partner Portalról a partneri központba – kereskedelmi piactér az Azure-hoz
description: Fiók áttelepítésének módja a CPP-ből a partneri központba. -Kereskedelmi piactér az Azure-hoz
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 8c7680896507cd3738fa3bce0d30a516d08509c4
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383290"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Fiók áttelepítése Cloud Partner Portalról a partneri központba

Ha az ajánlatokat Cloud Partner Portal (CPP) rendszerről a partner központba (PC) telepíti át, a rendszer zárolja őket a CPP-ben való szerkesztéshez. Ezen a ponton a Fiókbeállítások át kell telepíteni a partner központba. A fiók beállításai, valamint az ajánlatok is kezelhetők a partner Centerben.

## <a name="account-migration-process"></a>Fiók áttelepítési folyamata

Ha az ajánlatokat a CPP-ből telepíti át, a fiókja áttelepítésre van konfigurálva. 
 
Ha egy adott fiókhoz tartozó tulajdonosi szerepkörrel rendelkező felhasználó, a közzétevő profil lapján megjelenik egy sárga szalagcím. A rendszer arra kéri, hogy a Fiókbeállítások át legyenek áthelyezve a partneri központba. 

A fiók áttelepítési folyamatának elindításához kattintson a szalagcímre. A következő elemeket kell megadnia:

### <a name="work-email-address"></a>Munkahelyi e-mail cím

A legtöbb esetben jelentkezzen be a CPP-be való bejelentkezéshez használt e-mail-címmel. Bizonyos esetekben egy másik e-mail-címet kell használni:

* Microsoft-fiók: Ha a CPP-fiók egy Microsoft-fiók, akkor meg kell adnia egy érvényes munkahelyi e-mail-címet, amely ahhoz a bérlőhöz tartozik, amelyhez az MPN-azonosító regisztrálva van.

* Nem egyező bérlő: Ha a munkahelyi e-mail-címe nem tartozik a CPP-fiókjában található Microsoft Partner Network-AZONOSÍTÓhoz társított bérlőhöz, hibaüzenet jelenik meg. Ha ezt a hibát szeretné áthelyezni, adjon meg egy, a bérlőhöz társított e-mail-címet. A megjelenő hibaüzenet megadja a bérlő nevét.

### <a name="sign-up-for-microsoft-partner-network-program"></a>Regisztráció Microsoft Partner Network programra

Abban az esetben, ha a CPP-fiók nem rendelkezik Microsoft Partner Network-AZONOSÍTÓval, vagy ha az egyik érvénytelen, akkor az aktiválási folyamat részeként regisztrálnia kell a Microsoft Partner Network programra.

## <a name="after-account-migration-is-complete"></a>A fiók áttelepítésének befejezése után

Az áttelepítésnek egy adott fiók esetében csak egyszer kell történnie. Miután egy adott partner befejezte a fiók áttelepítését, az összes tulajdonos ezt a viselkedést látja a közzétevő profil lapján:

1. Ekkor megjelenik a partner-beállítások lap Microsoft Partner Network, ahol mostantól kezelheti a Fiókbeállítások beállításait. 
2. A rendszer a felhasználói szerepkörrel rendelkező felhasználók számára egy sárga szalagcímet jelenít meg a közzétevői profil lapján, amely arra kéri őket, hogy kezelje a fiók beállításait a partner Centerben.
3. A CPP Fiókbeállítások lapja írásvédett módba lesz konvertálva.

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
