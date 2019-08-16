---
title: Fiók áttelepítése Cloud Partner Portalról a partneri központba – kereskedelmi piactér az Azure-hoz
description: Fiók áttelepítésének módja a CPP-ből a partneri központba. -Kereskedelmi piactér az Azure-hoz
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/15/2019
ms.openlocfilehash: 9ab9a57641e6b34942ae6d4293311714177aa012
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533187"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Fiók áttelepítése Cloud Partner Portalról a partneri központba

Ha a rendszer áttelepíti az ajánlatokat a Cloud Partner Portalról (CPP) a partner központba (PC), a rendszer zárolja a CPP-ben való szerkesztéshez. Ezen a ponton a Fiókbeállítások át kell telepíteni a partner központba.  A fiók beállításai, valamint az ajánlatok is kezelhetők a partner Centerben.

## <a name="account-migration-process"></a>Fiók áttelepítési folyamata

Ha a rendszer áttelepíti az ajánlatokat a CPP-ből, a fiókja áttelepítésre van konfigurálva. 
 
Ha egy adott fiókhoz tartozó tulajdonosi szerepkörrel rendelkező felhasználó, a közzétevő profil lapján megjelenik egy sárga szalagcím.  A rendszer arra kéri, hogy helyezze át a Fiókbeállítások a partner központba. 

A fiók áttelepítési folyamatának elindításához kattintson a szalagcímre. A következő elemeket kell megadnia:

### <a name="work-e-mail-address"></a>**Munkahelyi e-mail cím**

A legtöbb esetben jelentkezzen be a CPP-be való bejelentkezéshez használt e-mail-címre. Bizonyos esetekben egy másik e-mail-címet kell használni:

* Microsoft-fiók: Ha a CPP-fiók egy Microsoft-fiók, akkor meg kell adnia egy érvényes munkahelyi e-mail-címet, amely ahhoz a bérlőhöz tartozik, amelyhez az MPN-azonosító regisztrálva van. 

* Nem egyező bérlő: Ha a munkahelyi e-mail-címe nem tartozik a CPP-fiókjában található Microsoft Partner Network-AZONOSÍTÓhoz társított bérlőhöz, hibaüzenet jelenik meg. Ha ezt a hibát szeretné áthelyezni, adjon meg egy, a bérlőhöz társított e-mail-címet. A megjelenő hibaüzenet megadja a bérlő nevét. 

### <a name="sign-up-for-microsoft-partner-network-program"></a>Regisztráció Microsoft Partner Network programra

Abban az esetben, ha a CPP-fiók nem rendelkezik Microsoft Partner Network-AZONOSÍTÓval, vagy ha az egyik érvénytelen, akkor az aktiválási folyamat részeként regisztrálnia kell a Microsoft Partner Network programra.

## <a name="account-activation-is-complete"></a>A fiók aktiválása befejeződött

A fiók áttelepítésének csak egyszer kell történnie egy adott fióknál. Miután egy adott partner befejezte a fiók áttelepítését, az összes tulajdonos ezt a viselkedést látja a közzétevő profil lapján:

1. Ekkor megtekintheti Microsoft Partner Network partneri Beállítások lapját, ahol kezelheti a Microsoft partner fiókjának beállításait. 
2. A fiók áttelepítése után a közzétevő profil lapján megjelenik egy sárga szalagcím, amely egy adott fiókhoz tartozó tulajdonosi szerepkörhöz tartozó felhasználók számára jelenik meg, kérve őket, hogy kezelje a fiók beállításait a partner Centerben. 
3. A rendszer ezután a CPP Fiókbeállítások lapját konvertálja írásvédett módba. 

## <a name="move-dynamics-365-based-solutions-to-partner-center"></a>Dynamics 365-alapú megoldások áthelyezése a partneri központba

Ha a Dynamics 365 for Customer engagement vagy a Dynamics 365 for Finance and Operations megoldás esetében az egyik kereskedelmi partner GTM-portálon van, az alábbi **utasítások szerint 2019. augusztus 31-ig ezeket az utasításokat** a megoldások a partner központba való áthelyezéséhez használja.

> [!NOTE]
> Ha a fiókja eredetileg a Partner tagsági központban (PMC) lett létrehozva, jelentkezzen be a [partner](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) központba, és erősítse meg, hogy a fiókja át lett telepítve az alábbi lépések végrehajtása előtt. Ha a profil képernyőjét az MPN-AZONOSÍTÓval látja, készen áll a folytatásra. Ha nem, el kell indítania a fiók áttelepítését a [Partner tagsági központban](https://partners.microsoft.com/partnerprogram/Welcome.aspx)megjelenő utasításokat követve. Ha segítségre van szüksége ezzel kapcsolatban, látogasson el a [támogatási szolgálatba](https://partner.microsoft.com/support?issueid=100-0077).

1. Lépjen a [kereskedelmi piactér – áttekintés lapra a partner Centerben](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Ha a bal oldali navigációs panelen a "kereskedelmi piactér" felirat látható, akkor a rendszer regisztrálja, és továbblép a következő lépésre. Ha nem, [Regisztráljon most a kereskedelmi piactéren](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) .
2. Az ajánlatait megkeresve erősítse [](https://appsource.microsoft.com/)meg ajánlatait a AppSource. Ha az ajánlatok már a AppSource-ben vannak, folytassa a következő lépéssel. A AppSource-ben nem szereplő bármely ajánlathoz hozzon létre egy [új dynamics 365](create-new-customer-engagement-offer.md) -et a Customer engagement ajánlathoz vagy egy [új Dynamics 365 for Operations ajánlatot](create-new-operations-offer.md).
3. Ellenőrizze a regisztrációját a Business Applications ISV-összekötő programban:
  
   * A partner Center [szerződések](https://partner.microsoft.com/dashboard/account/agreements) lapján ellenőrizze, hogy elfogadta-e a **Business Applications ISV** -kiegészítést a programban való regisztráláshoz.
   * A [Fiókbeállítások](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) lapon adja meg a számlázási adatokat.

4. Minden új és meglévő minősítési ajánlat beküldése, még akkor is, ha az ajánlatokat korábban már minősítették. **Javasoljuk, hogy a lehető leghamarabb küldje el a jóváhagyást, hogy elegendő idő legyen a jóváhagyásra a 2019. augusztus 31. előtt.**
5. Lépjen az [egyik kereskedelmi partner GTM](https://msgtm.azurewebsites.net/en-US/Profile/SignIn) -portálra, és adja hozzá a AppSource-lista URL-címét a Marketplace-hivatkozások szakaszban. Ha segítségre van szüksége ehhez a lépéshez, küldjön e cosell@microsoft.com-mailt a következő címre:.

## <a name="next-steps"></a>További lépések

- [A kereskedelmi piactér-fiók kezelése a partner Centerben](./manage-account.md) 
