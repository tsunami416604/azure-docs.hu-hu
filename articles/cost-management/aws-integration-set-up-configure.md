---
title: Állítsa be, és az AWS-költségek és a használati jelentés-integráció konfigurálása az Azure Cost Managementbe
description: Ez a cikk ismerteti, bár és AWS felhasználási és jelentés-integráció konfigurálása az Azure Cost Managementbe.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: e87e95ec9e4e20ee4785c2b1f448a7ca5f442b8a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409192"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Állítsa be, és az AWS-költségek és a használati jelentés-integráció konfigurálása

Az Amazon Web Services költsége és a használati jelentés-integrációnak köszönhetően figyelheti, és szabályozhatja az Azure Cost Management AWS költségeit. Az integráció lehetővé teszi, hogy egyetlen helyről az Azure Portalon, ahol nyomon követheti és költségeit az Azure és az AWS vezérlő. Ez a cikk ismerteti az integráció beállítása és beállítása úgy, hogy a költségek elemzése, és tekintse át a költségvetés használhatja a Cost Management szolgáltatásokat.

Felügyeleti folyamatok a jelentésdefiníciókat, és töltse le a jelentés a GZIP CSV-fájlok az AWS-hozzáférési hitelesítő adatok használatával egy S3 gyűjtő tárolt AWS-költségek és a használati jelentés költség.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Az AWS-ben felhasználási és jelentés létrehozása

Használja a felhasználási és jelentés, az AWS ajánlott módja gyűjtenek és dolgoznak fel az AWS-költségek. További információkért lásd: a [AWS-költségek és a használati jelentés](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) dokumentációs cikk.

Használja a **jelentések** felhasználási és jelentések létrehozásához a következő lépéseket az AWS-ben a számlázás és a Cost Management konzolra.

1. Jelentkezzen be az AWS-felügyeleti konzolon, és nyissa meg az elszámolással és Költségkezeléssel kezelőpultját https://console.aws.amazon.com/billing.
2. Kattintson a navigációs ablaktáblában **jelentések**.
3. Kattintson a **jelentés létrehozása**.
4. A **jelentés neve**, adja meg a jelentés nevét.
5. A **időegység**, válassza a **óránkénti**.
6. A **Belefoglalás**, adja hozzá az egyes erőforrások azonosítóit a jelentésben, és válassza ki **erőforrás-azonosítók**.
7. A **támogatásának engedélyezése**, nincs kijelölés nem szükséges.
8. A **adatfrissítési beállítások**, jelölje be **automatikusan frissítse a költségek &amp; használati jelentés díjak észlelése esetén az előző hónapra lezárt számlák**.
9. Kattintson a **tovább**.
10. A **Amazon S3 gyűjtőt**, írja be az Amazon S3 gyűjtőt, jelentés, ahová a nevét, és kattintson a **ellenőrizze**. A gyűjtőhöz, hogy érvényesek legyenek megfelelő engedélyekkel kell rendelkeznie. Engedélyek a gyűjtőbe történő hozzáadásával kapcsolatos további információkért lásd: [beállítása a Bucket és objektum-hozzáférési engedélyek](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html).
11. A **jelentés elérési út előtagja**, írja be a jelentés elérési út előtagja, amelyet hozzá kíván adni a jelentés nevét.
12. A **tömörítési**válassza **GZIP**.
13. Kattintson a **tovább**.
14. Miután áttekintette a a jelentéshez, kattintson a beállítások **tekintse át és fejezze be a**.
    Megjegyzés: a **jelentés neve**. Ez a későbbi lépésekben fogja használni.

A jelentések kézbesítése az Amazon S3 gyűjtő indításához az AWS akár 24 órát is igénybe vehet. Teljesítés indítása után a AWS naponta legalább egyszer frissíti az AWS-költségek és használati jelentések fájljait. Továbbra is az AWS-környezet konfigurálása a teljesítés indítása nélkül.

## <a name="create-a-role-and-policy-in-aws"></a>Hozzon létre egy szerepkör és a házirend az AWS-ben

Az Azure Cost Management fér hozzá az S3 gyűjtőt, ahol a felhasználási és jelentés megtalálható naponta több alkalommal. A Cost Management új adatok kereséséhez a hitelesítő adatokat hozzá kell férnie. Az AWS-ben, hogy engedélyezze a hozzáférést a Cost Management egy szerepkör és a szabályzat létrehozásához.

Ahhoz, hogy a szerepköralapú hozzáférés-, AWS-fiók, az Azure Cost Managementben, a szerepkör az AWS konzolon jön létre. Rendelkeznie kell a _szerepkör információ_ és _külső azonosító_ az AWS-konzolról. Később fogja használni őket a létrehozás egy AWS összekötő Oldal az Azure Cost Managementben.

Hozzon létre egy új szerepkör varázsló használata:

1. Jelentkezzen be az AWS konzolon, és válassza ki **szolgáltatások**.
2. A szolgáltatások listájában jelölje ki **IAM**.
3. Válassza ki **szerepkörök** majd **szerepkör létrehozása**.
4. A következő oldalon válassza ki a **egy másik AWS-fiók**.
5. A **Fiókazonosító** , adja meg _432263259397_.
6. A **beállítások**válassza **megkövetelése (ajánlott eljárás, egy harmadik féltől származó fogja feltételezni, hogy az ehhez a szerepkörhöz) külső azonosító**.
7. A **külső azonosító**, adja meg a külső azonosítója. A külső azonosító egy olyan közös kód, az AWS-szerepkör és az Azure Cost Management között. Az azonos külső azonosító is szolgál az új összekötő oldalon a Cost Management. Ha például egy külső azonosító hasonló _Companyname1234567890123_.
    Ne módosítsa a kijelölést azon **többtényezős hitelesítés megkövetelése**. Üresen kell hagyni.
8. Kattintson a **tovább: Engedélyek**.
9. Kattintson a **hozzon létre házirendet**. Ekkor megnyílik egy új böngészőlapon ahol létrehoz egy új szabályzatot.
10. Kattintson a **szolgáltatás kiválasztása**.

Költség- és használati jelentés engedély konfigurálása:

1. Típus **költség- és használati jelentés**.
2. Válassza ki **hozzáférési szint**, **olvasási** > **DescribeReportDefinitions**. Ez lehetővé teszi a Cost Management olvassa el, mi a PÉNZNEM az vannak definiálva, és döntse el, ha azok megfelelnek-e a jelentés definícióját előfeltételként szükséges szoftvert jelentések.
3. Kattintson a **adjon hozzá további engedélyek**.

Az S3 gyűjtő és az objektumok engedély konfigurálása:

1. Kattintson a **szolgáltatás kiválasztása**.
2. Típus _S3_.
3. Válassza ki **hozzáférési szint**, **lista** > **ListBucket**. Ez a művelet beolvassa az objektumok listájában, az S3 gyűjtőt a.
4. Válassza ki **hozzáférési szint**, **olvasási** > **GetObject**. Ez a művelet lehetővé teszi, hogy a számlázási fájlok letöltése.
5. Válassza ki **erőforrások**.
6. Válassza ki **gyűjtőbe – információ hozzáadása**.
7. A **gyűjtőbe neve**, adja meg a gyűjtő a PÉNZNEM fájlok tárolására használt.
8. Válassza ki **objektum – információ hozzáadása**.
9. A **gyűjtőbe neve**, adja meg a gyűjtő a PÉNZNEM fájlok tárolására használt.
10. A **objektumnév**válassza **bármely**.
11. Kattintson a **adjon hozzá további engedélyek**.

Költség Explorer engedély konfigurálása:

1. Kattintson a **szolgáltatás kiválasztása**.
2. Típus _Explorer szolgáltatás költsége_.
3. Válassza ki **költség Explorer az összes szolgáltatás műveletek (ce:\*)**. Ez a művelet ellenőrzi, hogy helyesen szerepel-e a gyűjteményben.
4. Kattintson a **adjon hozzá további engedélyek**.

Adja hozzá a szervezetek engedélyt:

1. Típus **szervezetek**.
2. Válassza ki **hozzáférési szintet, lista** > **ListAccounts**. Ez a művelet beolvassa a fiókok nevét.
3. A **tekintse át a házirend**, adja meg az új házirend nevét. Ellenőrizze, hogy ellenőrizze, hogy a helyes adatokat adott meg, és kattintson a **házirend létrehozása**.
4. Lépjen vissza az előző lapra, és frissítse a weblapot a böngészőben. A keresősávban keressen rá az új szabályzat.
5. Válassza ki **tovább: ellenőrzési**.
6. Adja meg az új szerepkör nevét. Ellenőrizze, hogy ellenőrizze, hogy a helyes adatokat adott meg, és kattintson a **szerepkör létrehozása**.
    Megjegyzés: a **szerepkör információ** és a **külső azonosító** a szerepkör létrehozásakor az előző lépésben használt. Később ezt fogja használni őket az Azure Cost Management-összekötő beállításakor.

A szabályzat JSON hasonlóan kell kinéznie a következő példa. Cserélje le _bucketname_ az S3 gyűjtő nevével.

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Az Azure-ban egy új AWS-összekötő beállítása

A következő információk használatával hozzon létre egy új AWS-összekötőt, és elkezdheti a monitorozást az AWS-költségek.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com.
2. Navigáljon a **Cost Management és számlázás** > **Cost Management**.
3. A **beállítások**, kattintson a **összekötők (előzetes verzió) a felhő**.  
    ![A felhő összekötők (előzetes verzió beállítás) megjelenítő példa](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Kattintson a **+ Hozzáadás** hozzon létre egy új összekötőt a lap tetején.
5. Egy AWS-összekötő lap létrehozását, írja be a **megjelenítendő név** az összekötő nevét.  
    ![Példa egy AWS-Connector-lap létrehozása](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Szükség esetén jelölje be az alapértelmezett felügyeleti csoportot. Az összes felderített összekapcsolt fiókok tárolja azt. Később is beállíthatja azt.
7. Alatt a **számlázási** szakaszban jelölje be **automatikusan díja szerint számítjuk fel az általános elérhetőség idején 1 %** Ha azt szeretné, folyamatos működés, ha lejár az előzetes verzióra. Ha az automatikus beállítást választja, jelöljön ki egy számlázási **előfizetés**.
8. Adja meg a **szerepkör információ**. Az értéke az AWS-ben a szerepkör beállításakor használt.
9. Adja meg a **külső azonosító**. Az értéke az AWS-ben a szerepkör beállításakor használt.
10. Adja meg a **jelentés neve** az AWS-ben létrehozott.
11. Kattintson a **tovább** majd **létrehozás**.

Az új AWS hatókörök, az AWS összevont fiók és a társított az AWS-fiókok és a költségadatok jelenik meg több órába is telhet.

Miután létrehozta az összekötőt, javasoljuk, hogy hozzáférés-vezérlés hozzárendelése az összekötőt. Felhasználói engedélyek vannak rendelve az újonnan felderített hatókörök: Az AWS-fiók és az AWS összevont fiókok csatolva. A felhasználót, hogy az összekötőt hoz létre az összekötőt, konszolidált fiók és minden hozzá kapcsolt fiókok tulajdonosa.

Összekötő engedélyeinek hozzárendelése felhasználókhoz felderítését követően nem engedélyek hozzárendelése a meglévő AWS-hatóköröket. Ehelyett csak az új csatolt fiókok kapnak engedélyt.

## <a name="additional-steps"></a>További lépések

- [Állítsa be a felügyeleti csoportok](../governance/management-groups/index.md#initial-setup-of-management-groups), ha még nem tette.
- Ellenőrizze, hogy új hatóköröket a Hatókörválasztó kerülnek. Ne felejtse el kattintson **frissítése** a legújabb adatok megjelenítéséhez.
- A Cloud connector oldalon válassza ki az összekötőt, és kattintson **Ugrás a számlázási fiók** a társított fiók hozzárendelése felügyeleti csoportokhoz.

## <a name="manage-cloud-connectors"></a>Felhőalapú összekötők kezelése

Amikor kiválaszt egy összekötőt, a felhő összekötők lapon, akkor a következőket teheti:

- Kattintson a **nyissa meg a fiók számlázási** AWS összevont fiók adatainak megtekintéséhez.
- Kattintson a **hozzáférés-vezérlés** kezelése a szerepkör-hozzárendelés az összekötőhöz.
- Kattintson a **szerkesztése** frissíteni az összekötőt. Az AWS-fiók száma nem módosítható, ahogyan az a szerepkör információ látható. Egy új összekötőt is létrehozhat.
- Kattintson a **ellenőrizze** kattintva futtassa újra az ellenőrzési tesztet annak biztosításához, hogy a Cost Management gyűjthet adatokat az összekötő-beállítások használatával.

![A példában létrehozott AWS-összekötők listájának megjelenítése](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Az Azure felügyeleti csoportok szerepe

Megtekintéséhez a több felhőre kiterjedő szolgáltató adatait egyetlen helyen, meg kell helyezni, az Azure-előfizetések és a társított AWS-fiókok ugyanabban a felügyeleti csoportban. Ha az Azure-környezet még nem konfigurált felügyeleti csoportokkal, lásd: [felügyeleti csoportok telepítése a kezdeti](../governance/management-groups/index.md#initial-setup-of-management-groups).

Ha azt szeretné, külön költségek, létrehozhat egy felügyeleti csoportot, amely csak a társított AWS-fiókok tárol.

## <a name="aws-consolidated-account"></a>Az AWS összevont fiók

Az AWS összevont fiók egyesíthetők a számlázással és fizetéssel több AWS-fiók szolgál. Az AWS összevont fiók egy AWS társított fiók is funkcionál.

![Az AWS konszolidált fiókadatok – példa](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Az oldal a következőket teheti:

- Kattintson a **frissítése** tömeges módosítása a felügyeleti csoport AWS társított hozzárendelését a fiókokat.
- Kattintson a **hozzáférés-vezérlés** a szerepkör-hozzárendelést a hatókör beállítása.

### <a name="aws-consolidated-account-permissions"></a>Az AWS összevont fiók engedélyei

Alapértelmezés szerint az AWS konszolidált fiók létrehozásakor, az AWS-összekötő engedélyei alapján vannak beállítva. Az összekötő létrehozó tulajdonosa.

A hozzáférési szintet az összevont AWS-fiók hozzáférési szint lapján kezelheti. Azonban az engedélyeket az AWS összevont fiók AWS összekapcsolt fiókok nem öröklik.

## <a name="aws-linked-account"></a>Az AWS összekapcsolt fiók

Az AWS társított fiók, ahol az AWS-erőforrások létrehozása és felügyelete. Társított fiók biztonsági határként is működik.

Ezen a lapon a következőket teheti:

- Kattintson a **frissítése** frissíteni a felügyeleti csoport AWS társított hozzárendelését a fiókhoz.
- Kattintson a **hozzáférés-vezérlés** szerepkör-hozzárendelés, a hatókör beállítása.

![Az AWS társított fiók oldal példája](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>Az AWS társított fiók engedélyei

Alapértelmezés szerint az AWS társított fiók engedélyek beállítása az AWS-összekötő engedélyei alapján létrehozásakor. Az összekötő létrehozó tulajdonosa. A hozzáférési szint az AWS társított fiók hozzáférési szint lapján kezelheti. Az AWS összevont fiók engedélyeit, AWS összekapcsolt fiókok nem öröklik.

Az AWS összekapcsolt fiókok mindig öröklik az engedélyeket a a felügyeleti csoporthoz tartoznak.

## <a name="next-steps"></a>További lépések

- Most, hogy állítsa be, és az AWS-költségek és a használati jelentés integrációs konfigurálva, folytassa [kezelése AWS-költségek és a használati](aws-integration-manage.md).
- Ha még nem ismeri a költségek elemzése, [vizsgálata és elemzése a költségeket a költségelemzés](quick-acm-cost-analysis.md) rövid.
- Ha még nem ismeri a költségvetés az Azure-ban, tekintse meg [létrehozása és kezelése az Azure költségvetése](tutorial-acm-create-budgets.md) oktatóanyag.
