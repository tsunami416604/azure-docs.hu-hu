---
title: Fiókáttelepítés a Felhőpartner-portálról a Partnerközpontba – az Azure kereskedelmi piactere
description: A fiók áttelepítése a CPP-ről a Partnerközpontba – az Azure kereskedelmi piactere
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 49fb2da05b7284fff10e6271de177551000995ed
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024421"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Fiók migrálása a Cloud Partner Portalról a Partnerközpontba

Ha meglévő Felhőpartner-portál (CPP) fiókkal rendelkezik, a fiókbeállításokat át kell telepítenie a Partnerközpontba.

## <a name="account-migration-process"></a>Fiók áttelepítési folyamata

Ha ön egy adott fiók CPP-jében tulajdonosi szerepkörrel rendelkező felhasználó, sárga szalagcím jelenik meg a Publisher-profil lapon. Ön az alábbi két eset egyikéhez tartozhat:

- Fiókját már áttelepítették, és készen áll a Fiókbeállítások kezelésére a Partnerközpontban.
- Fiókját nem telepítette át a Partnerközpontba, ezért további lépéseket kell tennie.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Fiókját áttelepítettük a Partnerközpontba

A CPP-ről a Partnerközpontra átszelő összes fiók esetében a fiókkezelés a Partnerközpontban történik. Az olyan módosítások, mint a felhasználó hozzáadása/törlése, a cpp-hez lesznek szinkronizálva.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Még nem telepítette át fiókját a Partnerközpontba

Kattintson a bannerre a fiók áttelepítési folyamatának elindításához. A következő elemeket kell megadnia:

1. Munkahelyi e-mail cím: <br> <br> A legtöbb esetben jelentkezzen be a CPP-be való bejelentkezéshez használt e-mail címmel. Bizonyos esetekben más e-mail címet kell használni:

    * Microsoft-fiók: Ha a CPP-fiók Microsoft-fiók, adjon meg egy érvényes munkahelyi e-mailt ahhoz a bérlőhöz társítva, akinek a Microsoft Partner Network (MPN) azonosítója regisztrálva van. További információt a [Regisztráció a Microsoft Partner Network Programprogramra című témakörben talál.](#sign-up-for-microsoft-partner-network-program)

    * Bérlői eltérés: Ha a munkahelyi e-mail-címe nem tartozik a CPP-fiókban található Microsoft Partner Network-azonosítóhoz társított bérlőhöz, akkor hibaüzenet jelenik meg. A hiba túllépése érdekében adjon meg egy e-mail címet a bérlőhöz társítva. Egy hibaüzenet adja meg a bérlő nevét.

2. Regisztráció a Microsoft Partner Network programra

    Ha cpp-fiókja nem rendelkezik Microsoft Partner Network-azonosítóval, vagy érvénytelen, akkor az aktiválási folyamat részeként regisztrálnia kell a Microsoft Partner Network programra.

## <a name="publishers-moving-from-cpp"></a>A CPP-ből átköltöző kiadók

Ha fiókját áttelepítették a [Felhőpartner-portálról (CPP),](https://cloudpartner.azure.com)nem kell új Partnerközpont-fiókot létrehoznia. A meglévő CPP-fiókba való bejelentkezés után személyre szabott hivatkozást kellett volna kapnia az új Partnerközpont-fiókjához, és szalaghirdetésben.

Miután engedélyezte új Partnerközpont-fiókját, és ellátogatta ezt a testreszabott hivatkozást, visszatérhet a fiókjához, ha felkeresi a [kereskedelmi piactér irányítópultját](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) a Partnerközpontban.

A közzétételi szerződés és a vállalati profil adatai átkerülnek az új Partnerközpont-fiókba, a korábban beállított fiókkifizetési profil adataival, a felhasználói fiókokkal és engedélyekkel, valamint a CPP-fiókhoz társított aktív ajánlatokkal együtt.

Miután a fiókadatait áthelyezte a CPP-ről a Partnerközpontba, a továbbiakban nem fogja a CPP-t fiókfrissítésekre használni, illetve a felhasználók, engedélyek és számlázás kezelésére használni. Korlátozott ideig a Partnerközpontban végzett fiókfrissítések automatikusan frissülnek az írásvédett CPP-fiókban, amíg a CPP-portál végül elavult.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Regisztráció a Microsoft Partner Network programra

Azoknak a vállalatoknak, amelyek a Microsofttal szeretnének együttműködni, csatlakozniuk kell a Microsoft Partner Networkhöz (MPN), és MPN-azonosítót kell kapniuk. Ha már tagja a Microsoft Partner Networknek, és rendelkezik MPN-azonosítóval, tartsa kéznél az információkat, ahogy a fiók aktiválási folyamata során szüksége lesz rájuk.  

Ha nem tagja a Microsoft Partner Networknek, [itt csatlakozhat](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) MPN-azonosítóért. Jegyezze fel az MPN-azonosítót, mert meg kell adnia a fiók aktiválási folyamata során.

Ha többet szeretne megtudni a Microsoft Partner Network hálózatról, olvassa [el a Csatlakozás a Microsoft Partner Network webhelyhez](https://partner.microsoft.com/en-US/membership) című témakört. Ha többet szeretne tudni a microsoftpartner-hálózat ban a hálózatépítő eszközekkel kapcsolatos előnyökről, olvassa el az [isv erőforrásközpontot.](https://partner.microsoft.com/isv-resource-hub)  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>A Dynamics 365 és a PowerApps ajánlatok áthelyezése a Partnerközpontba

A Dynamics 365 Customer Engagement, a PowerApps és a Dynamics 365 Operations fiók- és ajánlatkezelési rendszerének egyszerűsítése érdekében az ajánlatok átkerültek a [Partnerközpontba.](https://partner.microsoft.com/) A lépés biztosítja, hogy ugyanaza tartalom elérhető legyen mind a nyilvános, mind az eladói katalógusok számára.

A Dynamics 365 Customer Engagement, a PowerApps és a Dynamics 365 Operations ajánlatai esetében **2019.**

> [!NOTE]
> Ez nem vonatkozik a Dynamics 365 Business Central ajánlataira.  

1. Ha az MPN-tagsági fiókját eredetileg a Partner membership Centerben (PMC) hozták létre, jelentkezzen be a [Partnerközpontba,](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) és ellenőrizze, hogy a fiókját áttelepítették-e. Ha megjelenik egy profilképernyő az MPN-azonosítóval, készen áll a folytatásra. Ha nem, akkor a [partnertagsági központban](https://partners.microsoft.com/partnerprogram/Welcome.aspx)található utasításokat követve kell elindítania a fiók áttelepítését. Ha segítségre van szüksége, látogasson el [a támogatási .](https://partner.microsoft.com/support?issueid=100-0077)
2. Nyissa meg a [Kereskedelmi piactér áttekintése lapot a Partnerközpontban.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Ha a bal oldali navigációs ablakban a "Kereskedelmi piactér" jelenik meg, akkor regisztrált, és a következő lépéssel folytatja. Ha nem, [beiratkozik a kereskedelmi piacon](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) most.
3. Erősítse meg az ajánlatok at AppSource [keres az ajánlatokat.](https://appsource.microsoft.com/) Ha az ajánlatok már az AppSource-ban vannak, folytassa a következő lépéssel. Az AppSource-ban nem található bármely ajánlathoz hozzon létre egy [új Dynamics 365 Customer Engagement ajánlatot](create-new-customer-engagement-offer.md) vagy egy [új Dynamics 365 Operations ajánlatot.](create-new-operations-offer.md)
4. A Partnerközpont [Megállapodások lapján](https://partner.microsoft.com/dashboard/account/agreements)ellenőrizze, hogy áttekintette-e és elfogadta-e az **üzleti alkalmazások isv-függelékét.**
5. A Partnerközpont [fiókbeállításaiban](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)győződjön meg arról, hogy a számlázási adatok teljesek.
6. Minden új és meglévő ajánlatot bekell küldeni e minősítésre és közzétételre, még akkor is, ha az ajánlatok korábban hitelesek voltak.
    * Töltse ki az információs képernyőket, beleértve az alkalmazás hitelesítésre való biztosítását, valamint a marketinginformációkat. **2019. október 15-ig**válassza a **Küldés** (a képernyő jobb felső sarkában) lehetőséget. Ezeket a lépéseket el kell végezni, hogy ne befolyásolja az ajánlat rendelkezésre állását.
    * Ha jogosult, kérheti, hogy a folyamat során vegyen részt a prémium csomagban.
    * A minősítéshez vagy az újratanúsításhoz az alkalmazásnak támogatnia kell az üzleti alkalmazások platformjának legújabb verzióját.
    * Az alkalmazás jóváhagyását követően e-mailt kap az ajánlathoz való visszatéréshez, és válassza az "élő adás" lehetőséget, hogy az ajánlat élesben jelenjen meg a Microsoft AppSource szolgáltatásban.

## <a name="additional-resources"></a>További források

Segítséget kaphat a fórumok szakértőitől és szakértőitől, és blogokat, webináriókat, videókat, eseményeket és egyebeket fedezhet fel a [Microsoft Dynamics CRM webhelyen.](https://community.dynamics.com/crm?wa=wsignin1.0)

Ha segítségre van szüksége a piactéri ajánlatok közzétételéhez, hitelesítéséhez vagy kezeléséhez, [küldjön be egy támogatási jegyet.](https://partner.microsoft.com/support/v2/?stage=1)

## <a name="next-step"></a>Következő lépés

- [Kereskedelmi piactéri fiók kezelése a Partnerközpontban](./manage-account.md)
