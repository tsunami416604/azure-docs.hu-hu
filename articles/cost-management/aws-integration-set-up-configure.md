---
title: Állítsa be, és az AWS-költségek és a használati jelentés-integráció konfigurálása az Azure Cost Managementbe
description: Ez a cikk végigvezeti és AWS felhasználási és jelentés-integráció konfigurálása az Azure Cost Managementbe.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 951178a82e0975f5f2af71bd48cf0f931246ae37
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002125"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Állítsa be, és az AWS-költségek és a használati jelentés-integráció konfigurálása

Az Amazon Web Services (AWS) felhasználási és jelentés (PÉNZNEM) integrációs figyelheti, és szabályozhatja az Azure Cost Management AWS költségeit. Az integráció lehetővé teszi, hogy egyetlen helyről az Azure Portalon, ahol nyomon követheti és költségeit az Azure és az AWS vezérlő. Ez a cikk bemutatja az integráció beállítása, és konfigurálja azt, hogy az Azure Cost Management szolgáltatásokat költségek elemzése, és tekintse át a költségvetés használhat.

Felügyeleti folyamatok a jelentésdefiníciókat, és töltse le a jelentés a GZIP CSV-fájlok az AWS-hozzáférési hitelesítő adatok használatával egy S3 gyűjtő tárolt AWS-költségek és a használati jelentés költség.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Az AWS-ben felhasználási és jelentés létrehozása

Az AWS-javasolt módja gyűjtenek és dolgoznak fel az AWS-költségek felhasználási és jelentés használata. További információkért lásd: a [AWS-költségek és a használati jelentés](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) dokumentációját.

Használja a **költség- és használati jelentések** a számlázás és a Cost Management konzolra, az AWS-ben felhasználási és jelentések létrehozásához az alábbi lépéseket követve:

1. Jelentkezzen be az AWS-felügyeleti konzolon, és nyissa meg a [elszámolással és Költségkezeléssel konzol](https://console.aws.amazon.com/billing).
2. A navigációs panelen válassza ki **költség- és használati jelentések**.
3. Válassza ki **jelentés létrehozása**.
4. A **jelentés neve**, adja meg a jelentés nevét.
5. A **további részletekről** , hogy az tartalmazza az egyes erőforrások azonosítóit a jelentést, majd válassza a **erőforrás-azonosítókat tartalmazza**.
6. A **adatfrissítési beállítások**, válassza ki, hogy az AWS felhasználási és AWS vonatkozik visszatérítés, kreditek, ha a jelentés vagy a számla véglegesítése után támogatja a díjak a fiókjához. Amikor frissíti a jelentést, Amazon S3 feltöltött egy új jelentést. Javasoljuk, hogy hagyja ezt a beállítást.
7. Kattintson a **Tovább** gombra.
8. A **S3 gyűjtőt**, válassza a **konfigurálása**.
9. S3 gyűjtőt konfigurálja párbeszédpanelen tegye a következők egyikét:
    1. Egy meglévő időszakot válasszon a legördülő listából, majd válassza **tovább**.
    2. Adja meg a gyűjtőbe nevét és a régióban, ahol szeretné hozzon létre egy új gyűjtő **tovább**.
10. Válassza ki a I megerősítette, hogy van-e ez a szabályzat javítsa ki, és válassza a mentés lehetőséget.
11. (Nem kötelező) A jelentés elérési út előtagja adja meg a jelentés elérési útja a jelentés nevére prepended kívánt előtagot.
Ne adjon meg egy előtagot, ha az alapértelmezett előtag a 4. lépésben a jelentésben és a dátumtartományt, a jelentés a következő formátumban megadott nevét: `/report-name/date-range/`
12. A **időegység**, válassza a **óránkénti**.
13. A **jelentés versioning**, válassza ki, hogy a jelentés felülírni a jelentés előző verzióját, vagy a korábbi verziók mellett kézbesítendő minden verziója.
14. A **data-integráció engedélyezése**, nincs kijelölés nem szükséges.
15. A **tömörítési**válassza **GZIP**.
16. Kattintson a **Tovább** gombra.
17. Miután áttekintette a beállításokat a jelentéshez, válassza ki a **tekintse át és fejezze be a**.

    Megjegyzés: a jelentés nevét. A későbbi lépésekben használni.

A jelentések kézbesítése az Amazon S3 gyűjtő indításához az AWS akár 24 órát is igénybe vehet. Teljesítés indítása után a AWS naponta legalább egyszer frissíti az AWS-költségek és használati jelentések fájljait. Továbbra is az AWS-környezet konfigurálása a teljesítés indítása nélkül.

## <a name="create-a-role-and-policy-in-aws"></a>Hozzon létre egy szerepkör és a házirend az AWS-ben

Az Azure Cost Management fér hozzá az S3 gyűjtőt, ahol a felhasználási és jelentés megtalálható naponta több alkalommal. A szolgáltatást új adatok kereséséhez a hitelesítő adatokat hozzá kell férnie. Egy szerepkör és a házirendet hoz létre az AWS-ben, hogy a Költségkezelés az elérésére.

Szerepköralapú hozzáférés-, AWS-fiók, a Cost Management engedélyezéséhez a szerepkört az AWS konzolon jön létre. Rendelkeznie kell a _szerepkör információ_ és _külső azonosító_ az AWS-konzolról. Később, használja azokat a a **az AWS-összekötő létrehozása** Cost Management oldalán.

Hozzon létre egy új szerepkör varázsló használata:

1. Jelentkezzen be az AWS konzolon, és válassza ki **szolgáltatások**.
2. A szolgáltatások listájában jelölje ki **IAM**.
3. Válassza ki **szerepkörök** majd **szerepkör létrehozása**.
4. A következő oldalon válassza ki a **egy másik AWS-fiók**.
5. A **Fiókazonosító**, adja meg **432263259397**.
6. A **beállítások**válassza **megkövetelése (ajánlott eljárás, egy harmadik féltől származó fogja feltételezni, hogy az ehhez a szerepkörhöz) külső azonosító**.
7. A **külső azonosító**, adja meg a külső azonosítója. A külső azonosító egy olyan közös kód, az AWS-szerepkör és az Azure Cost Management között. Az azonos külső azonosító is megtalálható a **új összekötő** Cost Management oldalán. Ha például egy külső azonosító hasonló _Companyname1234567890123_.

    > [!NOTE]
    > Ne módosítsa a kijelölést azon **többtényezős hitelesítés megkövetelése**. Üresen kell hagyni.
8. Válassza ki **tovább: Engedélyek**.
9. Válassza ki **hozzon létre házirendet**. Ekkor egy új böngészőlap jelenik meg. Ez az, ahol létrehozhat egy olyan szabályzatot.
10. Válassza ki **szolgáltatás kiválasztása**.

Adja meg a költségek és a használati jelentés engedélyt:

1. Adja meg **költség- és használati jelentés**.
2. Válassza ki **hozzáférési szint** > **olvasási** > **DescribeReportDefinitions**. Ez a lépés lehetővé teszi, hogy a Cost Management segítségével olvassa el, milyen PÉNZNEM jelentéseket definiált, és döntse el, ha azok megfelelnek-e a jelentés definícióját előfeltételként szükséges szoftvert.
3. Válassza ki **adjon hozzá további engedélyek**.

Az S3 gyűjtőt és objektumokra vonatkozó engedély konfigurálása:

1. Válassza ki **szolgáltatás kiválasztása**.
2. Adja meg **S3**.
3. Válassza ki **hozzáférési szint** > **lista** > **ListBucket**. Ez a művelet beolvassa az objektumok listájában, az S3 gyűjtőt a.
4. Válassza ki **hozzáférési szint** > **olvasási** > **GetObject**. Ez a művelet lehetővé teszi, hogy a számlázási fájlok letöltését.
5. Válassza ki **erőforrások**.
6. Válassza ki **gyűjtőbe – információ hozzáadása**.
7. A **gyűjtőbe neve**, adja meg a gyűjtő a PÉNZNEM fájlok tárolására használt.
8. Válassza ki **objektum – információ hozzáadása**.
9. A **gyűjtőbe neve**, adja meg a gyűjtő a PÉNZNEM fájlok tárolására használt.
10. A **objektumnév**válassza **bármely**.
11. Válassza ki **adjon hozzá további engedélyek**.

Engedély költség Explorer konfigurálása:

1. Válassza ki **szolgáltatás kiválasztása**.
2. Adja meg **Explorer szolgáltatás költsége**.
3. Válassza ki **költség Explorer az összes szolgáltatás műveletek (ce:\*)**. Ez a művelet ellenőrzi, hogy helyesen szerepel-e a gyűjteményben.
4. Válassza ki **adjon hozzá további engedélyek**.

Adja hozzá az AWS-szervezetek számára engedélyt:

1. Adja meg **szervezetek**.
2. Válassza ki **hozzáférési szint** > **lista** > **ListAccounts**. Ez a művelet beolvassa a fiókok nevét.
3. A **tekintse át a házirend**, adja meg az új házirend nevét. Ellenőrizze, hogy helyes információt adott-e, és válassza ki **házirend létrehozása**.
4. Lépjen vissza az előző lapra, és a böngésző weblap frissítése. A keresősávba, a keresse meg az új szabályzat.
5. Válassza ki **tovább: ellenőrzési**.
6. Adja meg az új szerepkör nevét. Ellenőrizze, hogy helyes információt adott-e, és válassza ki **szerepkör létrehozása**.

    Megjegyzés: a szerepkör információ és a külső azonosító használható az előző lépésekben a szerepkör létrehozásakor. Fogja használni őket később az Azure Cost Management-összekötő beállításakor.

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

Az alábbi információk segítségével hozzon létre egy AWS-összekötőt, és elkezdheti a monitorozást az AWS-költségek:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen a **Cost Management és számlázás** > **Cost Management**.
3. A **beállítások**válassza **összekötők (előzetes verzió) a felhő**.  
    ![A felhő összekötők (előzetes verzió) megjelenítő példa beállítás)](./media/aws-integration-setup-configure/cloud-connectors-preview01.png).
4. Válassza ki **+ Hozzáadás** hozzon létre egy összekötőt a lap tetején.
5. Az a **az AWS-összekötő létrehozása** lap **megjelenítendő név**, adja meg az összekötő nevét.  
    ![Példa az oldal az AWS-összekötő létrehozása](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Szükség esetén jelölje be az alapértelmezett felügyeleti csoportot. Az összes felderített összekapcsolt fiókok tárolja azt. Később is beállíthatja azt.
7. Az a **számlázási** szakaszban jelölje be **automatikusan díja szerint számítjuk fel az általános elérhetőség idején 1 %** Ha azt szeretné, folyamatos működés, ha lejár az előzetes verzióra. Ha az automatikus beállítást választja, jelöljön ki egy számlázási előfizetés.
8. A **szerepkör információ**, írja be az értéket, amelyet a szerepkör az AWS-ben beállításakor használt.
9. A **külső azonosító**, írja be az értéket, amelyet a szerepkör az AWS-ben beállításakor használt.
10. A **jelentés neve**, adja meg a nevét, az AWS-ben létrehozott.
11. Válassza ki **tovább** majd **létrehozás**.

Előfordulhat, hogy az új AWS-hatókörök néhány órát is igénybe, az AWS összevont fiók és a társított AWS-fiókok és a költséget adatok jelennek meg.

Miután létrehozta az összekötőt, azt javasoljuk, hogy Ön által hozzárendelt hozzáférés-vezérlés. Felhasználói engedélyek vannak rendelve az újonnan felderített hatókörök: Az AWS konszolidált fiók és a társított AWS-fiókok. A felhasználó, aki a-összekötőt hoz létre, az összekötőt, az összevont fiók és minden társított fiók tulajdonosának.

Összekötő engedélyeinek hozzárendelése felhasználókhoz felderítését követően nem engedélyek hozzárendelése a meglévő AWS-hatóköröket. Ehelyett csak az új csatolt fiókok kapnak engedélyt.

## <a name="take-additional-steps"></a>További lépések

- [Állítsa be a felügyeleti csoportok](../governance/management-groups/index.md#initial-setup-of-management-groups), ha még nem tette.
- Ellenőrizze, hogy új hatóköröket a Hatókörválasztó kerülnek. Válassza ki **frissítése** a legújabb adatok megjelenítéséhez.
- Az a **összekötők Felhőbeli** lapon válassza ki az összekötőt, és válassza **Ugrás a számlázási fiók** a társított fiók hozzárendelése felügyeleti csoportokhoz.

## <a name="manage-cloud-connectors"></a>Felhőalapú összekötők kezelése

Amikor kiválaszt egy összekötőt a a **összekötők Felhőbeli** lapon is:

- Válassza ki **nyissa meg a fiók számlázási** összevont fiók az AWS információinak megtekintése.
- Válassza ki **hozzáférés-vezérlés** kezelése a szerepkör-hozzárendelés az összekötőhöz.
- Válassza ki **szerkesztése** frissíteni az összekötőt. Az AWS számát, nem módosítható, mivel az információ szerepkör megjelenik. Azonban létrehozhat egy új összekötőt.
- Válassza ki **ellenőrizze** kattintva futtassa újra az ellenőrzési tesztet, győződjön meg arról, hogy Cost Management gyűjtheti az összekötő-beállítások használatával.

![A példában létrehozott AWS az összekötők listája](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Az Azure felügyeleti csoportok beállítása

Megtekintéséhez a több felhőre kiterjedő szolgáltató adatait egyetlen helyen, meg kell helyezni, az Azure-előfizetések és a társított AWS-fiókok ugyanabban a felügyeleti csoportban. Ha az Azure-környezet még nem konfigurált felügyeleti csoportokkal, lásd: [felügyeleti csoportok telepítése a kezdeti](../governance/management-groups/index.md#initial-setup-of-management-groups).

Ha azt szeretné, külön költségek, létrehozhat egy felügyeleti csoportot, amely csak a társított AWS-fiókok tárol.

## <a name="set-up-an-aws-consolidated-account"></a>Állítsa be az AWS összevont fiók

Az összevont AWS-fiókon egyesíti a számlázással és fizetéssel több AWS-fiókok esetében. Társított AWS-fiók is funkcionál.

![Példa részleteit egy AWS összevont fiók](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

A lapon a következőket teheti:

- Válassza ki **frissítése** tömeges frissítése AWS közötti társítás társított fiókok olyan felügyeleti csoporttal.
- Válassza ki **hozzáférés-vezérlés** a szerepkör-hozzárendelést a hatókör beállítása.

### <a name="permissions-for-an-aws-consolidated-account"></a>Az AWS engedélyeinek összevont fiók

Alapértelmezés szerint az összevont AWS-fiók engedélyeit a fiók létrehozásakor, az AWS-összekötő engedélyei alapján vannak beállítva. Az összekötő létrehozó tulajdonosa.

A hozzáférési szint használatával kezelheti a **hozzáférési szint** az AWS lapján összevont fiók. Azonban az AWS kapcsolódó fiókok nem öröklik az engedélyeket az összevont AWS-fiókjába.

## <a name="set-up-an-aws-linked-account"></a>Az AWS-beli társított fiók beállítása

Az AWS-beli társított fiók, ahol az AWS-erőforrások létrehozása és felügyelete. Társított fiók biztonsági határként is működik.

Ezen az oldalon a következőket teheti:

- Válassza ki **frissítése** frissíteni egy AWS közötti társítás társított a-fiókot egy felügyeleti csoporttal.
- Válassza ki **hozzáférés-vezérlés** szerepkör-hozzárendelés, a hatókör beállítása.

![Az AWS társított fiók oldal példája](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Az AWS engedélyeinek társított fiókot

Alapértelmezés szerint az engedélyek a társított AWS-fiók létrehozásakor, az AWS-összekötő engedélyei alapján vannak beállítva. Az összekötő létrehozó tulajdonosa. A hozzáférési szint használatával kezelheti a **hozzáférési szint** az AWS lapján társított fiókot. Az AWS kapcsolódó fiókok nem öröklik az engedélyeket a konszolidált AWS-fiók.

Az AWS kapcsolódó fiókok mindig öröklik az engedélyeket a felügyeleti csoportból, melynek tagja.

## <a name="next-steps"></a>További lépések

- Most, hogy állítsa be, és az AWS-költségek és a használati jelentés integrációs konfigurálva, folytassa [kezelése AWS-költségek és a használati](aws-integration-manage.md).
- Ha még nem ismeri a költségek elemzése, [vizsgálata és elemzése a költségeket a költségelemzés](quick-acm-cost-analysis.md) rövid.
- Ha még nem ismeri a költségvetés az Azure-ban, tekintse meg [létrehozása és kezelése az Azure költségvetése](tutorial-acm-create-budgets.md).
