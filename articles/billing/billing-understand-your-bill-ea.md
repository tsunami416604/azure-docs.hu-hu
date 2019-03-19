---
title: Számlázással kapcsolatos tudnivalók az Azure Enterprise |} A Microsoft Docs
description: Ismerje meg az Azure-nagyvállalati szerződéssel rendelkező ügyfelek a használatának és számlájának értelmezése
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: banders
ms.openlocfilehash: 16feb41707e5afa07054d20b620f5822eedad773
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858912"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>Számlázással kapcsolatos Azure-nagyvállalati szerződéssel rendelkező ügyfelek számára

Azure-nagyvállalati szerződéssel rendelkező ügyfelek igazoló számla haladhatja meg a munkahelyi kredit és használata a szolgáltatások, a kredit nem vonatkozik. 

A munkahelyi kredit a pénzügyi kötelezettségvállalását is tartalmaz. A pénzügyi kötelezettségvállalás az a szervezet Azure-szolgáltatások használati előre fizetett összeg. A nagyvállalati szerződésbe fiókbeli vállalt díjkeretet kapcsolatba lép a Microsoft ügyfélmenedzserével vagy viszonteladójával adhat hozzá.  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>Ha túllépte, illetve nem vonatkozik

Kap egy vagy több számlákat, ha az alábbiak történnek:

- **Szolgáltatási kerettúllépés**: A szervezet használati díjak a kreditegyenlegét lehet.
- **Külön számlázott díjak**: A szolgáltatások, a szervezet használja a kredit nem vonatkozik. Függetlenül az kreditegyenlegét számlázott van a következő szolgáltatásokat:
    - Canonical
    - Citrix XenApp Essentials
    - A Citrix xendesktop-példányok 
    - Regisztrált felhasználó
    - Openlogic
    - Távoli hozzáférési jogok a XenApp Essentials regisztrált felhasználó
    - Ubuntu Advantage
    - A Visual Studio Enterprise (havi)
    - Visual Studio Enterprise (Annual)
    - A Visual Studio Professional (havonta)
    - Visual Studio Professional (Annual)
- **Piactér-díjak**: Az Azure piactéren történt vásárlást és használatát a munkahelyi kredit nem vonatkozik, és külön számlázzuk. A vállalati rendszergazda engedélyezheti vagy letilthatja a cégük számára a vállalati portálon a piactéren történő vásárlások képes. 

Ha rendelkezik esedékes díjak szolgáltatási kerettúllépés és a számlázási időszak alatt külön-külön számlázott díjak, egy mindkét típusú díjakat tartalmazó számlát kap. Piactér-díjak mindig számlázása külön történik.

## <a name="review-charges"></a>A költségek áttekintése

Tekintse át, és ellenőrizze a díjait a számlán, a vállalati rendszergazdának kell lennie. További információkért lásd: [rendszergazdai szerepkörök az Azure nagyvállalati szerződés ismertetése az Azure-ban](billing-understand-ea-roles.md). Ha nem tudja, aki a vállalati rendszergazda engedélyezte a szervezete számára [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Szolgáltatási szint keretét túllépő számla felülvizsgálata

Hasonlítsa össze a felhasználás teljes összege, a vállalati portálról **jelentések** > **használati összegzés** a szolgáltatási keretet túllépő számla. A szolgáltatás kerettúllépési számlán tartalmaz, amely meghaladja a munkahelyi kredit felhasználást és/vagy a szolgáltatások, a kredit nem vonatkozik. Összegek az **használati összegzés** nem tartalmazzák a forgalmi adót. 

1. Jelentkezzen be a [az Enterprise portal](https://ea.azure.com).
1. Válassza ki **jelentések**.
1. A lap jobb felső sarokban a Váltás a nézet **M** való **C** , majd az az időszak, a számlán.
 
   ![Képernyőkép a M + használati összegzés C lehetőséget.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. A **teljes felhasználás** összeg meg kell egyeznie a **kiterjesztett mennyiségén** a szolgáltatás kerettúllépési számlán. Az alábbi táblázatban a használati jelenik meg a számlán és a leírások a **használati összegzés** a vállalati portálon:

   |Számla kifejezés|Használati összesítő kifejezés|Leírás|
   |---|---|---|
   |Kiegészítő összeg összesen|Összes használat|A teljes előtti használati díjat a jóváírás alkalmazása előtt a meghatározott időszakra vonatkozóan.|
   |Kötelezettségvállalás használat|Kötelezettségvállalás használat|A kreditek adott időszak alatt a alkalmazni.|
   |Teljes értékesítés|Kerettúllépés összesen|A teljes felhasználás díja, amely meghaladja a kreditösszeg erejéig. Ez a mennyiség nem tartalmazzák a forgalmi adót.|
   |Adó összege|Nem alkalmazható|Áfa, amely a teljes értékesítési összeg az adott időszakra vonatkozik.|
   |Teljes összeg|Nem alkalmazható|Az összeg miután alkalmazta a kreditek, és adó bekerül a számla esedékessége.|
1. További információ a díjakról, keresse fel **használati adatok letöltése** > **speciális jelentés letöltése**. Ez a jelentés nem tartalmazza a adók vagy foglalások kell fizetni, vagy a piactér-díjak.

      ![Képernyőkép a speciális jelentés töltse le a letöltés használati lapon.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>Marketplace-számlában áttekintése

Hasonlítsa össze az Azure Marketplace-en található összes **jelentések** > **használati összegzés** a vállalati portál a marketplace-számlában. A piactéri számlán csak az Azure Marketplace-beli vásárlások és használat szerepel. Összegek az **használati összegzés** nem tartalmazzák a forgalmi adót. 

1. Jelentkezzen be a [az Enterprise portal](https://ea.azure.com).
1. Válassza ki **jelentések**.
1. A lap jobb felső sarokban a Váltás a nézet **M** való **C** , majd az az időszak, a számlán.

     ![Képernyőkép a M + használati összegzés C lehetőséget.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. A **Azure Marketplace-en** összesen meg kell egyeznie a **teljes értékesítés** a marketplace-számlában.
1. További információ a használat alapú költségek, keresse fel **használati adatok letöltése**. A **piactér-díjak**válassza **letöltése**. Ez a jelentés nem tartalmazzák az adókat, vagy egyszeri vásárlások megjelenítése.

     ![Képernyőkép a töltse le a piactér-díjak lehetőséget.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
