---
title: Az AWS és a használati jelentés integrációjának beállítása és konfigurálása Azure Cost Management
description: Ez a cikk bemutatja, hogyan állíthatja be és konfigurálja az AWS-t és a használati jelentéseket a Azure Cost Management-integrációval.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 66dbe45ff1a8ee773fdf7fcb0aa7cfe8e6ad6437
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219696"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Az AWS és a használati jelentés integrálásának beállítása és konfigurálása

A Amazon Web Services (AWS) Cost és a használati jelentés (akt) integrálásával az AWS-kiadásokat a Azure Cost Managementban figyelheti és irányíthatja. Az integráció lehetővé teszi, hogy a Azure Portal egyetlen helyen figyelje és szabályozza az Azure és az AWS kiadásait. Ez a cikk bemutatja, hogyan állíthatja be az integrációt, és hogyan konfigurálhatja, hogy Azure Cost Management szolgáltatások használatával elemezze a költségeket és tekintse át a költségvetést.

Cost Management feldolgozza az AWS-gyűjtőn tárolt és használati jelentést az AWS-hozzáférési hitelesítő adatokkal, hogy lekérje a jelentések definícióit, és letöltse a jelentés GZIP CSV-fájljait.

## <a name="create-a-cost-and-usage-report-in-aws"></a>Cost-és használati jelentés létrehozása az AWS-ben

A Cost és a használati jelentés használata az AWS által ajánlott módszer az AWS-költségek gyűjtésére és feldolgozására. További információ: [AWS Cost and használati jelentés](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) dokumentációja.

Az AWS számlázási és Cost Management konzoljának **cost & használati jelentések** lapján a következő lépésekkel hozhat létre költségeket és használati jelentéseket:

1. Jelentkezzen be az AWS felügyeleti konzolra, és nyissa meg a [számlázási és Cost Management-konzolt](https://console.aws.amazon.com/billing).
2. A navigációs ablaktáblán válassza a **Cost & használati jelentések**elemet.
3. Válassza a **jelentés létrehozása**lehetőséget.
4. A **jelentés neve**mezőben adja meg a jelentés nevét.
5. A **további jelentés részletei**területen válassza az **erőforrás-azonosítók belefoglalása**lehetőséget.
6. Az **adatfrissítési beállítások beállításnál**válassza ki, hogy az AWS költség-és használati jelentését szeretné-e frissíteni, ha az AWS a számla véglegesítése után a fiókra vonatkozó visszatérítést, jóváírást vagy támogatási díjat alkalmaz. Amikor egy jelentés frissül, egy új jelentés kerül feltöltésre az Amazon S3-ba. Javasoljuk, hogy hagyja bejelölve a beállítást.
7. Kattintson a **Tovább** gombra.
8. Az **S3 gyűjtőhöz**válassza a **Konfigurálás**lehetőséget.
9. Az S3 gyűjtő konfigurálása párbeszédpanelen hajtsa végre az alábbi műveletek egyikét:
    1. Válasszon ki egy meglévő gyűjtőt a legördülő listából, és válassza a **tovább**lehetőséget.
    2. Adja meg a gyűjtő nevét és a régiót, ahol létre kíván hozni egy új gyűjtőt, majd válassza a **tovább**lehetőséget.
10. Jelölje be az **Megerősítem, hogy a szabályzat helyes**, majd kattintson a **Mentés**gombra.
11. Választható A jelentés elérési útjának előtagjaként adja meg azt a jelentés elérési útját, amelyet előtagértéke szeretne a jelentés nevére.
Ha nem ad meg előtagot, az alapértelmezett előtag a jelentéshez megadott név. A dátumtartomány `/report-name/date-range/` formátummal rendelkezik.
12. Az **időegység**beállításnál válassza az **óránként**lehetőséget.
13. A **jelentések verziószámozása**beállításnál válassza ki, hogy szeretné-e, hogy a jelentés minden verziója felülírja az előző verziót, vagy ha további új jelentéseket szeretne.
14. Az **Adatintegráció engedélyezéséhez**nincs szükség kijelölésre.
15. A **tömörítéshez**válassza a **gzip**lehetőséget.
16. Kattintson a **Tovább** gombra.
17. Miután áttekintette a jelentés beállításait, válassza a **felülvizsgálat és Befejezés**lehetőséget.

    Jegyezze fel a jelentés nevét. Ezt a későbbi lépések során fogja használni.

Akár 24 óráig is eltarthat, amíg az AWS elkezdi jelentéseket kézbesíteni az Amazon S3-gyűjtőnek. A kézbesítést követően az AWS naponta legalább egyszer frissíti az AWS-költségeket és a használati jelentéseket tartalmazó fájlokat. Folytathatja az AWS-környezet konfigurálását, és nem kell megvárnia a kézbesítés megkezdését.

## <a name="create-a-role-and-policy-in-aws"></a>Szerepkör és szabályzat létrehozása az AWS-ben

Azure Cost Management hozzáfér az S3 gyűjtőhöz, ahol a Cost és a használati jelentés naponta többször is megtalálható. A szolgáltatásnak hozzá kell férnie a hitelesítő adatokhoz az új adatok ellenőrzéséhez. Az AWS-ben létre kell hoznia egy szerepkört és egy házirendet, amely lehetővé teszi Cost Management számára a hozzáférését.

Ahhoz, hogy szerepköralapú hozzáférést lehessen engedélyezni egy AWS-fiókhoz Cost Managementban, a szerepkör az AWS-konzolon jön létre. Az AWS-konzolon rendelkeznie kell az ARN és a _External ID_ _szerepkörrel_ . Később ezeket a Cost Management az AWS- **összekötő létrehozása** oldalon használhatja.

Az új szerepkör létrehozása varázsló használata:

1. Jelentkezzen be az AWS-konzolra, és válassza a **szolgáltatások**lehetőséget.
2. A szolgáltatások listájában válassza a **iam**lehetőséget.
3. Válassza ki a **szerepkörök** elemet, majd válassza a **szerepkör létrehozása**lehetőséget.
4. A következő oldalon válasszon **egy másik AWS-fiókot**.
5. A **fiók azonosítója**mezőben adja meg a **432263259397**értéket.
6. A **Beállítások**területen jelölje be a **külső azonosító megkövetelése (ajánlott eljárás, ha egy harmadik fél ezt a szerepkört fogja feltételezni)** .
7. A **külső azonosító**mezőben adja meg a külső azonosítót. A külső azonosító az AWS szerepkör és a Azure Cost Management közötti közös PIN-kód. Ugyanez a külső azonosító is használatban van a Cost Management **új összekötő** lapján. Egy külső azonosító például a _Companyname1234567890123_hasonlít.

    > [!NOTE]
    > Ne módosítsa az **MFA megkövetelése**beállítást. Továbbra is törölni kell.
8. Válassza a **Tovább: engedélyek**lehetőséget.
9. Válassza a **házirend létrehozása**lehetőséget. Ekkor egy új böngészőlap jelenik meg. Itt hozhat létre egy szabályzatot.
10. Válassza **a szolgáltatás kiválasztása**lehetőséget.

A Cost és a használati jelentés engedélyeinek konfigurálása:

1. Adja meg **a költségeket és a használati jelentést**.
2. Válassza a **hozzáférési szint** > **olvasás** > **DescribeReportDefinitions**lehetőséget. Ez a lépés lehetővé teszi Cost Management számára, hogy elolvassa, hogy milyen aktuális jelentések vannak meghatározva, és hogy azok megfelelnek-e a jelentésdefiníció előfeltételeinek.
3. Válassza a **további engedélyek hozzáadása**lehetőséget.

Az S3 gyűjtő és objektumok engedélyeinek konfigurálása:

1. Válassza **a szolgáltatás kiválasztása**lehetőséget.
2. Adja meg az **S3**értéket.
3. Válassza a **hozzáférési szint** > **lista** > **ListBucket**lehetőséget. Ez a művelet beolvassa az S3 gyűjtőben található objektumok listáját.
4. Válassza a **hozzáférési szint** > **olvasás** > **GetObject**lehetőséget. Ez a művelet engedélyezi a számlázási fájlok letöltését.
5. Válassza az **erőforrások**lehetőséget.
6. Válassza a **gyűjtő – ARN hozzáadása**elemet.
7. A **gyűjtő neve**mezőbe írja be az aktuális fájlok tárolására szolgáló gyűjtőt.
8. Válassza az **objektum – ARN hozzáadása**elemet.
9. A **gyűjtő neve**mezőbe írja be az aktuális fájlok tárolására szolgáló gyűjtőt.
10. Az **Objektumnév**mezőben válassza **a bármelyik**lehetőséget.
11. Válassza a **további engedélyek hozzáadása**lehetőséget.

A Cost Explorer engedélyének konfigurálása:

1. Válassza **a szolgáltatás kiválasztása**lehetőséget.
2. Adja meg a **Cost Explorer szolgáltatást**.
3. Válassza ki **az összes Cost Explorer szolgáltatási műveletet (CE:\*)** . Ez a művelet ellenőrzi, hogy a gyűjtemény helyes-e.
4. Válassza a **további engedélyek hozzáadása**lehetőséget.

Engedély hozzáadása AWS-szervezetekhez:

1. Adja meg a **szervezeteket**.
2. Válassza a **hozzáférési szint** > **lista** > **ListAccounts**lehetőséget. Ez a művelet lekéri a fiókok nevét.
3. A **felülvizsgálati szabályzat**mezőben adja meg az új szabályzat nevét. Győződjön meg arról, hogy a megfelelő adatokat adta meg, majd válassza a **házirend létrehozása**lehetőséget.
4. Lépjen vissza az előző lapra, és frissítse a böngésző weblapját. Keresse meg az új szabályzatot a keresősáv alatt.
5. Válassza a **Next (tovább): felülvizsgálat**lehetőséget.
6. Adja meg az új szerepkör nevét. Győződjön meg arról, hogy a megfelelő adatokat adta meg, majd válassza a **szerepkör létrehozása**lehetőséget.

    Jegyezze fel az ARN szerepkört és az előző lépésekben használt külső azonosítót a szerepkör létrehozásakor. Ezeket később a Azure Cost Management-összekötő beállításakor fogja használni.

A szabályzat JSON-nek az alábbi példához hasonlónak kell lennie. Cserélje le az _bucketname_ -t az S3 gyűjtő nevére.

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

## <a name="set-up-a-new-aws-connector-in-azure"></a>Új AWS-összekötő beállítása az Azure-ban

Az alábbi információk használatával AWS-összekötőt hozhat létre, és megkezdheti az AWS-költségek figyelését:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Lépjen **Cost Management + számlázási** > **Cost Management**.
3. A **Beállítások**területen válassza a **Cloud Connectors (előzetes verzió)** lehetőséget.  
    ![például a Cloud Connectors (előzetes verzió) beállítást](./media/aws-integration-setup-configure/cloud-connectors-preview01.png)).
4. Összekötő létrehozásához kattintson a lap tetején a **+ Hozzáadás** gombra.
5. Az **AWS-összekötő létrehozása** oldalon a **megjelenítendő név**mezőben adja meg az összekötő nevét.  
    ![példa az AWS-összekötő létrehozásához](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. Szükség esetén az alapértelmezett felügyeleti csoportot is kiválaszthatja. Az összes felderített csatolt fiókot fogja tárolni. Később is beállíthatja.
7. A **számlázás** szakaszban válassza **az 1%-os automatikus feltöltés az általános rendelkezésre állás** esetén lehetőséget, ha az előnézet lejárta után is biztosítani szeretné a folyamatos működést. Ha az automatikus beállítást választja, ki kell választania egy számlázási előfizetést.
8. Az **ARN szerepkör**esetében adja meg a szerepkör AWS-ben való beállításakor használt értéket.
9. A **külső azonosító**mezőben adja meg a szerepkör AWS-ben való beállításakor használt értéket.
10. A **jelentés neve**mezőbe írja be az AWS-ben létrehozott nevet.
11. Válassza a **tovább** , majd a **Létrehozás**lehetőséget.

Az új AWS-hatókörök, az AWS-összevont fiók, az AWS-vel összekapcsolt fiókok és a költségadatok megjelenése is eltarthat néhány óráig.

Az összekötő létrehozása után azt javasoljuk, hogy rendeljen hozzá hozzáférés-vezérlést. A felhasználók engedélyeket kapnak az újonnan felderített hatókörökhöz: AWS összevont fiók és AWS-hez kapcsolt fiókok. Az összekötőt létrehozó felhasználó az összekötő, a konszolidált fiók és az összes társított fiók tulajdonosa.

Az összekötő engedélyeinek a felhasználóknak való kiosztása a felderítés után nem rendel hozzá engedélyeket a meglévő AWS-hatókörökhöz. Ehelyett csak az új társított fiókok kapnak engedélyeket.

## <a name="take-additional-steps"></a>További lépések elvégzése

- [Felügyeleti csoportok beállítása](../governance/management-groups/overview.md#initial-setup-of-management-groups), ha még nem tette meg.
- Győződjön meg arról, hogy az új hatókörök hozzá lettek adva a hatókör-választóhoz. Kattintson a **frissítés** elemre a legfrissebb adatértékek megtekintéséhez.
- A **Felhőbeli összekötők** lapon válassza ki az összekötőt, és válassza az **Ugrás a számlázási fiókra** lehetőséget a társított fiók felügyeleti csoportokhoz való hozzárendeléséhez.

## <a name="manage-cloud-connectors"></a>Felhőbeli összekötők kezelése

Amikor kijelöl egy összekötőt a **felhőalapú összekötők** lapon, a következőket teheti:

- Válassza az **Ugrás a számlázási fiókra** lehetőséget az AWS konszolidált fiók adatainak megtekintéséhez.
- Válassza a **Access Control** lehetőséget az összekötő szerepkör-hozzárendelésének kezeléséhez.
- Az összekötő frissítéséhez válassza a **Szerkesztés** lehetőséget. Az AWS-fiók száma nem módosítható, mert az az ARN szerepkörben jelenik meg. Létrehozhat azonban egy új összekötőt is.
- Válassza az **ellenőrzés** lehetőséget az ellenőrzési teszt újrafuttatásához, hogy a Cost Management képes legyen adatokat gyűjteni az összekötő-beállítások használatával.

![Példa a létrehozott AWS-összekötők listájára](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="set-up-azure-management-groups"></a>Azure felügyeleti csoportok beállítása

Helyezze el az Azure-előfizetéseit és AWS-fiókját ugyanabban a felügyeleti csoportban, és hozzon létre egyetlen helyet, ahol megtekintheti a több felhőalapú szolgáltató adatait. Ha még nem konfigurálta az Azure-környezetet felügyeleti csoportokkal, tekintse meg a [felügyeleti csoportok kezdeti beállítását](../governance/management-groups/overview.md#initial-setup-of-management-groups)ismertető témakört.

Ha szeretné elkülöníteni a költségeket, létrehozhat egy olyan felügyeleti csoportot, amely csak AWS-hez kapcsolódó fiókokat tartalmaz.

## <a name="set-up-an-aws-consolidated-account"></a>AWS konszolidált fiók beállítása

Az AWS konszolidált fiók több AWS-fiók esetében a számlázást és a fizetést ötvözi. Ez egy AWS-hez csatolt fiók is.

![Példa egy AWS konszolidált fiókra](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

Az oldalon a következőket teheti:

- Válassza a **frissítés** lehetőséget, ha az AWS-hez csatolt fiókok társítását felügyeleti csoporttal szeretné tömegesen frissíteni.
- Válassza a **Access Control** lehetőséget a hatókör szerepkör-hozzárendelésének beállításához.

### <a name="permissions-for-an-aws-consolidated-account"></a>Az AWS konszolidált fiók engedélyei

Alapértelmezés szerint az AWS-összevont fiók engedélyei a fiók létrehozásakor vannak beállítva az AWS-összekötő engedélyei alapján. Az összekötő létrehozója a tulajdonos.

A hozzáférési szintet az AWS konszolidált fiók **hozzáférési szint** lapján kezelheti. Az AWS-hez csatolt fiókok azonban nem öröklik az AWS konszolidált fiókra vonatkozó engedélyeket.

## <a name="set-up-an-aws-linked-account"></a>AWS-hez kapcsolódó fiók beállítása

Az AWS-hez kapcsolódó fiók az AWS-erőforrások létrehozása és kezelése. Egy csatolt fiók biztonsági határként is működik.

Ezen a lapon a következőket teheti:

- Válassza a **frissítés** lehetőséget, ha egy AWS-hez csatolt fiók társítását szeretné frissíteni egy felügyeleti csoporttal.
- A hatókörhöz tartozó szerepkör-hozzárendelés beállításához válassza a **Access Control** lehetőséget.

![Példa az AWS-hez csatolt fiók oldalára](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="permissions-for-an-aws-linked-account"></a>Az AWS-hez csatolt fiók engedélyei

Alapértelmezés szerint az AWS-hez csatolt fiók engedélyei a létrehozás után, az AWS-összekötő engedélyei alapján vannak beállítva. Az összekötő létrehozója a tulajdonos. A hozzáférési szintet az AWS-hez csatolt fiók **hozzáférési szint** lapján kezelheti. Az AWS-hez csatolt fiókok nem öröklik az AWS konszolidált fiók engedélyeit.

Az AWS-hez csatolt fiókok mindig öröklik az engedélyeket attól a felügyeleti csoporttól, amelyhez tartoznak.

## <a name="next-steps"></a>Következő lépések

- Most, hogy beállította és konfigurálta az AWS költség-és használati jelentés integrációját, folytassa az [AWS költségeinek és használatának kezelésével](aws-integration-manage.md).
- Ha nem ismeri a költségek elemzését, tekintse meg [a Cost Analysis gyors üzembe helyezés és a költségek elemzése](quick-acm-cost-analysis.md) című témakört.
- Ha nem ismeri a költségvetést az Azure-ban, tekintse meg az [Azure-költségvetések létrehozását és kezelését](tutorial-acm-create-budgets.md)ismertető témakört.
