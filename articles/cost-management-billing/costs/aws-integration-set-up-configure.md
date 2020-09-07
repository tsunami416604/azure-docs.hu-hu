---
title: Az AWS és az Azure Cost Management integrációjának beállítása
description: Ez a cikk végigvezeti az AWS költség- és használati jelentésnek az Azure Cost Managementbe való integrálásának beállításán és konfigurálásán.
author: bandersmsft
ms.author: banders
ms.date: 08/28/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.openlocfilehash: 8bf3df25d4702b4a0cc6361f20ad08e618e7d62b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266088"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>Az AWS költség- és használati jelentés integrálásának beállítása és konfigurálása

Az Amazon Web Services (AWS) költség- és használati jelentésének (CUR) integrálásával az AWS-kiadások az Azure Cost Managementben figyelhetővé és szabályozhatóvá válnak. Az integráció lehetővé teszi, hogy az Azure Portalon egyetlen helyről egyszerre lehessen monitorozni és szabályozni az Azure-ral és az AWS-szel kapcsolatos kiadásokat. Ez a cikk bemutatja, hogyan lehet az integrációt beállítani és konfigurálni ahhoz, hogy az Azure Cost Management-funkciókkal elemezhetők és áttekinthetők legyenek a költségvetések.

A Cost Management úgy dolgozza fel az AWS költség- és használati jelentését, amely egy S3-tárolóban található, hogy az Ön AWS-hozzáférési hitelesítő adatait használja a jelentésdefinícióik lekéréséhez és a jelentések GZIP CSV-fájljainak letöltéséhez.

Ha többet szeretne megtudni az AWS jelentésintegrációjának beállításáról, tekintse meg [az AWS-összekötők Cost Managementben történő beállításával](https://www.youtube.com/watch?v=Jg5KC1cx5cA) foglalkozó videót. További videók megtekintéséhez látogasson el a [Cost Management YouTube-csatornájára](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/Jg5KC1cx5cA]

## <a name="create-a-cost-and-usage-report-in-aws"></a>Költség- és használati jelentés létrehozása az AWS-ben

A költség- és használati jelentés használata az AWS által javasolt módszer az AWS költségeinek összegyűjtésére és feldolgozására. További információkért lásd az [AWS költség- és használati jelentésének](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html) dokumentációját.

Az AWS számlázási és Cost Management-konzoljának **költség- és használati jelentésekkel** foglalkozó oldalán a következő lépésekkel hozhat létre költségeket és használati jelentéseket:

1. Jelentkezzen be az AWS felügyeleti konzolra, és nyissa meg a [Billing and Cost Management console](https://console.aws.amazon.com/billing) (Számlázási és költségkezelési konzolt).
2. A navigációs sávon válassza a **Cost & Usage Reports** (Költség- és használati jelentések) elemet.
3. Válassza a **Create report** (Jelentés létrehozása) parancsot.
4. A **Report name** (Jelentés neve) mezőben adja meg a jelentés nevét.
5. Az **Additional report details** (Jelentés további részletei) területen válassza az **Include resource IDs** (Erőforrás-azonosítók belefoglalása) lehetőséget.
6. A **Data refresh settings** (Adatfrissítési beállítások) alatt válassza ki, szeretné-e, hogy az AWS költség- és használati jelentés frissüljön, ha az AWS visszatérítéseket, jóváírásokat vagy támogatási díjakat alkalmaz az Ön fiókjára, miután a számla véglegesítve lett. Egy jelentés frissítésekor egy új jelentés lesz feltöltve az Amazon S3-ba. Javasoljuk, hogy ezt a beállítást hagyja bejelölve.
7. Kattintson a **Tovább** gombra.
8. Az **S3 bucket** (S3-tárolónál) esetén válassza a **Configure** (Konfigurálás) parancsot.
9. Az S3-tároló konfigurálására szolgáló párbeszédpanelen végezze el a következő feladatok egyikét:
    1. Válasszon ki egy meglévő tárolót a legördülő listából, majd válassza a **Next** (Tovább) lehetőséget.
    2. Adja meg a tároló nevét és a régiót, ahol létre kívánja hozni az új tárolót, majd kattintson a **Next** (Tovább) gombra.
10.    Jelölje be az **I have confirmed that this policy is correct** (Megerősítem, hogy ez a szabályzat helyes) lehetőséget, majd kattintson a **Save** (Mentés) parancsra.
11.    (Nem kötelező) A jelentés elérési útjának előtagjánál adja meg a jelentés nevének elejéhez hozzáadott előtagot.
Ha nem ad meg előtagot, a rendszer az alapértelmezett előtagot tekinti a jelentéshez megadott névnek. A dátumtartomány formátuma: `/report-name/date-range/`.
12. A **Time unit** (Időegység) beállításnál válassza az **Hourly** (Óránként) lehetőséget.
13.    A **Report versioning** (Jelentés verziószámozása) beállításnál válassza ki, hogy szeretné-e, hogy a jelentés minden egyes verziója felülírja az előző verziót, vagy további új jelentéseket szeretne létrehozni.
14. Az **Enable data integration for** (Adatintegráció engedélyezése a következőhöz) beállításnál semmit nem kell kiválasztania.
15. A **Compression** (Tömörítés) beállításnál válassza a **GZIP** lehetőséget.
16. Kattintson a **Tovább** gombra.
17. Miután átnézte a jelentése beállításait, válassza a **Review and Complete** (Áttekintés és befejezés) lehetőséget.

    Jegyezze fel a jelentés nevét. A későbbi lépésekben szükség lesz rá.

Akár 24 órát is igénybe vehet, hogy az AWS kézbesíteni kezdje a jelentéseket az Amazon S3-tárolóba. A kézbesítés megkezdése után az AWS legalább naponta egyszer frissíti az AWS költség- és használati jelentésének fájljait. Folytathatja az AWS-környezet konfigurálását anélkül, hogy meg kellene várnia a kézbesítés megkezdését.

## <a name="create-a-role-and-policy-in-aws"></a>Szerepkör és szabályzat létrehozása az AWS-ben

Az Azure Cost Management naponta többször eléri azt az S3-tárolót, ahol a költség- és használati jelentés található. A szolgáltatásnak hozzáférésre van szüksége a hitelesítő adatokhoz, hogy ellenőrizni tudja az új adatok elérhetőségét. Ehhez az AWS-ben létre kell hozni egy szerepkört és egy szabályzatot.

Ahhoz, hogy egy AWS-fiók szerepköralapú hozzáférését lehetővé tegyük a Cost Managementben, a szerepkört az AWS-konzolon kell létrehozni. Ehhez szükség van a _szerepkör ARN-re_ és a _külső azonosítóra_ az AWS-konzolról. Később ezeket az **AWS-összekötő létrehozására** szolgáló lapon fogjuk használni a Cost Managementben.

Használja az új szerepkör létrehozására szolgáló varázslót:

1. Jelentkezzen be az AWS-konzolra és válassza a **Services** (Szolgáltatások) elemet.
2. A szolgáltatások listájában válassza az **IAM** elemet.
3. Válassza a **Roles** (Szerepkörök), majd a **Create Role** (Szerepkör létrehozása) lehetőséget.
4. A következő lapon válassza az **Another AWS account** (Másik AWS-fiók) lehetőséget.
5. Az **Account ID** (Fiókazonosító) mezőben adja meg a következőt: **432263259397**.
6. Az **Options** (Beállítások) között válassza a következő lehetőséget: **Require external ID (Best practice when a third party will assume this role)** (Külső azonosító megkövetelése (Ajánlott eljárás, ha a szerepkört egy harmadik fél használja)).
7. Az **External ID** (Külső azonosító) mezőben adja meg a külső azonosítót, amely egy jelszó, melyet az AWS-szerepkör és az Azure Cost Management közösen használ. Ugyanezt a külső azonosítót kell megadni a Cost Managementben az **Új összekötő** lapon is. A Microsoft egy erős jelszószabályzat használatát javasolja a külső azonosító megadásakor.
    > [!NOTE]
    > Ne módosítsa a **Require MFA** (Többtényezős hitelesítés megkövetelése) részt. Ez a lehetőség maradjon bejelöletlen.
8. Válassza a **Next: Permissions** (Tovább: Engedélyek) lehetőséget.
9. Válassza a **Create policy** (Szabályzat létrehozása) lehetőséget. Ekkor egy új böngészőlap jelenik meg. Itt hozhat létre egy új szabályzatot.
10. Válassza a **Choose a service** (Szolgáltatás kiválasztása) lehetőséget.

Konfigurálja a költség- és használati jelentésre vonatkozó engedélyeket:

1. Írja be a **Cost and Usage Report** kifejezést.
2. Válassza az **Access level** > **Read** > **DescribeReportDefinitions** (Hozzáférési szint > Olvasás > DescribeReportDefinitions) elemet. Ez a lépés lehetővé teszi, hogy a Cost Management beolvassa, hogy milyen CUR-jelentések vannak meghatározva, és megállapítsa, hogy ezek megfelelnek-e a jelentésdefiníció előfeltételeinek.
3. Válassza az **Add additional permissions** (További engedélyek hozzáadása) lehetőséget.

Konfigurálja az S3-tárolóra és -objektumokra vonatkozó engedélyeket:

1. Válassza a **Choose a service** (Szolgáltatás kiválasztása) lehetőséget.
2. Írja be az **S3** kifejezést.
3. Válassza az **Access level** > **List** > **ListBucket** (Hozzáférési szint > Felsorolás > ListBucket) elemet. Ez a művelet lekéri az S3-tárolóban található objektumok listáját.
4. Válassza az **Access level** > **Read** > **GetObject** (Hozzáférési szint > Olvasás > GetObject) elemet. Ez a művelet lehetővé teszi a számlázási fájlok letöltését.
5. Válassza a **Resources** (Erőforrások) elemet.
6. Válassza a **bucket – Add ARN** (tároló – ARN hozzáadása) elemet.
7. A **Bucket name** (Tároló neve) mezőben adja meg a CUR-fájlok tárolására használt tárolót.
8. Válassza az **object – Add ARN** (objektum – ARN hozzáadása) elemet.
9. A **Bucket name** (Tároló neve) mezőben adja meg a CUR-fájlok tárolására használt tárolót.
10. Az **Object name** (Objektum neve) beállításnál válassza az **Any** (Bármelyik) lehetőséget.
11. Válassza az **Add additional permissions** (További engedélyek hozzáadása) lehetőséget.

Konfigurálja a Cost Explorerre vonatkozó engedélyeket:

1. Válassza a **Choose a service** (Szolgáltatás kiválasztása) lehetőséget.
2. Írja be a **Cost Explorer Service** kifejezést.
3. Válassza az **All Cost Explorer Service actions (ce:\*)** (Összes Cost Explorer szolgáltatási műveletet) lehetőséget. Ez a művelet ellenőrzi, hogy a gyűjtemény helyes-e.
4. Válassza az **Add additional permissions** (További engedélyek hozzáadása) lehetőséget.

AWS-szervezetek engedélyének hozzáadása:

1. Írja be az **Organizations** kifejezést.
2. Válassza az **Access level** > **List** > **ListAccounts** (Hozzáférési szint > Felsorolás > ListAccounts) elemet. Ez a művelet lekéri a fiókok nevét.
3. A **Review Policy** (Szabályzat áttekintése) résznél adja meg az új szabályzat nevét. Ellenőrizze, hogy helyesen adta-e meg az adatokat, majd válassza a **Create Policy** (Szabályzat létrehozása) parancsot.
4. Lépjen vissza az előző lapra, és frissítse az oldalt a böngészőben. A keresőmezőben keressen rá az új szabályzatra.
5. Válassza a **Next: Review** (Tovább: Áttekintés) lehetőséget.
6. Adja meg az új szerepkör nevét. Ellenőrizze, hogy helyesen adta-e meg az adatokat, majd válassza a **Create Role** (Szerepkör létrehozása) parancsot.

    Jegyezze fel a szerepkör ARN-jét és a külső azonosítót, amelyeket az előző lépések során használt a szerepkör létrehozásához. Ezekre később szükség lesz az Azure Cost Management összekötőjének beállításakor.

A szabályzat JSON-jának a következő példához hasonlóan kell kinéznie. Cserélje le a _bucketname_ részt a saját S3-tárolójának a nevére.

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

## <a name="set-up-a-new-connector-for-aws-in-azure"></a>Új AWS-összekötő beállítása az Azure-ban

Az alábbiak alapján adhat hozzá egy új AWS-összekötőt, és kezdheti meg a saját AWS-költségei monitorozását:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen a **Költségkezelés + számlázás** > **Cost Management** elemre.
3. A **Beállítások** területen válassza az **AWS-összekötők** elemet.  
4. A lap tetején válassza a **+Hozzáadás** lehetőséget az összekötő létrehozásához.  
    :::image type="content" source="./media/aws-integration-setup-configure/aws-connector.png" alt-text="Az AWS-összekötők beállítását bemutató példa" :::
1. Az **Összekötő létrehozása** lapon, a **Megjelenítendő név** mezőben adjon egy nevet az összekötőnek.  
    :::image type="content" source="./media/aws-integration-setup-configure/create-aws-connector01.png" alt-text="Példa az AWS-összekötő létrehozására szolgáló lapra" :::
1. Igény szerint válassza ki az alapértelmezett felügyeleti csoportot. Ez fogja tárolni az összes felderített összekapcsolt fiókot. Ezt később is beállíthatja.
1. A **számlázási** szakaszban állítsa az **Automatikus megújítás** beállítást **Be** értékre, ha biztosítani szeretné a folyamatos működést. Ha az automatikus beállítást választja, ki kell választania egy előfizetést a számlázáshoz.
1. A **Szerepkör ARN-je** mezőben adja meg ugyanazt az értéket, amelyet a szerepkör beállításához használt az AWS-ben.
1. A **Külső azonosító** mezőben adja meg ugyanazt az értéket, amelyet a szerepkör beállításához használt az AWS-ben.
1. A **Jelentés neve** mezőben adja meg az AWS-ben létrehozott nevet.
1. Válassza a **Tovább**, majd a **Létrehozás** parancsot.

Beletelhet néhány órába, mire az új AWS-hatókörök, az összesített AWS-fiók, az összekapcsolt AWS-fiókok és az ezekre vonatkozó költségadatok megjelennek.

Miután létrehozta az összekötőt, javasoljuk, hogy rendeljen hozzá hozzáférés-vezérlést. A felhasználók az újonnan felderített hatókörökhöz kapnak engedélyeket: az összesített AWS-fiókhoz és az összekapcsolt AWS-fiókokhoz. Az összekötőt létrehozó felhasználó lesz az összekötő, az összesített fiók és az összes összekapcsolt fiók tulajdonosa.

Ha a felderítés után a felhasználókhoz összekötő-engedélyeket rendel, ennek során nem rendel engedélyeket a meglévő AWS-hatókörökhöz. Ehelyett csak az új összekapcsolt fiókokhoz lesznek engedélyek rendelve.

## <a name="take-additional-steps"></a>További lépések

- [Hozza létre a felügyeleti csoportokat](../../governance/management-groups/overview.md#initial-setup-of-management-groups), ha ez még nem történt meg.
- Ellenőrizze, hogy az új hatókörök hozzáadódnak-e a hatókörválasztóhoz. Kattintson a **Frissítés** parancsra a legújabb adatok megtekintéséhez.
- A **Felhőbeli összekötők** lapon válassza ki az összekötőt, és kattintson az **Ugrás a számlázási fiókra** lehetőségre az összekapcsolt fiók felügyeleti csoportokhoz való társításához.

> [!NOTE]
> A felügyeleti csoportok a Microsoft Ügyfélszerződéssel (MCA) rendelkező ügyfelek esetében jelenleg nem támogatottak. Az MCA-ügyfelek létrehozhatják az összekötőt és megtekinthetik AWS-adataikat. Az MCA-ügyfelek azonban nem tekinthetik meg egyszerre az Azure- és az AWS-költségeiket egy felügyeleti csoport esetében.

## <a name="manage-aws-connectors"></a>AWS-összekötők felügyelete

Amikor kiválaszt egy összekötőt a **AWS-összekötők** lapon, a következőket teheti:

- Kiválasztja az **Ugrás a számlázási fiók** lehetőséget a konszolidált AWS-fiók adatainak megtekintéséhez.
- Kiválasztja a **Hozzáférés-vezérlés** lehetőséget az összekötő szerepkör-hozzárendeléseinek kezeléséhez.
- Kiválasztja a **Szerkesztés** lehetőséget az összekötő frissítéséhez. Az AWS-fiók száma nem módosítható, mert az szerepel a szerepkör ARN-jében. De létrehozhat egy új összekötőt.
- Válassza az **Ellenőrzés** lehetőséget az ellenőrzési teszt újrafuttatásához, ami megvizsgálja, hogy a Cost Management képes-e az adatok gyűjtésére az összekötő beállításaival.

:::image type="content" source="./media/aws-integration-setup-configure/aws-connector-details.png" alt-text="Példa AWS-összekötőre – részletek" :::

## <a name="set-up-azure-management-groups"></a>Azure-beli felügyeleti csoportok beállítása

Helyezze el az Azure-előfizetéseit és összekapcsolt AWS-fiókjait ugyanabban a felügyeleti csoportban, hogy egyetlen helyen láthatóvá váljanak a különböző felhőszolgáltatók adatai. Ha még nem konfigurálta az Azure-környezetét a felügyeleti csoportokkal, lásd: [A felügyeleti csoportok kezdeti beállítása](../../governance/management-groups/overview.md#initial-setup-of-management-groups).

Ha el szeretné különíteni a költségeket, létrehozhat egy olyan felügyeleti csoportot, amely csak az összekapcsolt AWS-fiókokat tartalmazza.

## <a name="set-up-an-aws-consolidated-account"></a>Összesített AWS-fiók beállítása

Az összesített AWS-fiók magában egyesíti több AWS-fiók számlázását és fizetését. Emellett összekapcsolt AWS-fiókként is funkcionál. Az összesített AWS-fiók részleteit az AWS-összekötő oldalán található hivatkozásra kattintva tekintheti meg. 

:::image type="content" source="./media/aws-integration-setup-configure/aws-consolidated-account01.png" alt-text="Példa egy összesített AWS-fiók részleteire" :::

Az oldalon a következőket teheti:

- Kiválasztja a **Frissítés** lehetőséget, amellyel tömegesen frissítheti az összekapcsolt AWS-fiókok társítását egy felügyeleti csoporthoz.
- Kiválasztja a **Hozzáférés-vezérlés** lehetőséget a hatókörhöz tartozó szerepkör-hozzárendelések beállításához.

### <a name="permissions-for-an-aws-consolidated-account"></a>Egy összesített AWS-fiók engedélyei

Alapértelmezés szerint az összesített AWS-fiókok engedélyei a fiók létrehozásakor az AWS-összekötő engedélyei alapján vannak beállítva. A tulajdonos az összekötő létrehozója.

A hozzáférési szintet az összesített AWS-fiók **Hozzáférési szint** lapján lehet kezelni. Az összekapcsolt AWS-fiókok azonban nem öröklik az összesített AWS-fiók engedélyeit.

## <a name="set-up-an-aws-linked-account"></a>Összekapcsolt AWS-fiók beállítása

Az összekapcsolt AWS-fiókban történik az AWS-erőforrások létrehozása és kezelése. Az összekapcsolt fiók emellett biztonsági határként is funkcionál.

Ezen az oldalon a következőket teheti:

- Kiválasztja a **Frissítés** lehetőséget, amellyel frissítheti egy összekapcsolt AWS-fiók társítását egy felügyeleti csoporthoz.
- Kiválasztja a **Hozzáférés-vezérlés** lehetőséget a hatókörhöz tartozó szerepkör-hozzárendelések beállításához.

:::image type="content" source="./media/aws-integration-setup-configure/aws-linked-account01.png" alt-text="Példa egy összekapcsolt AWS-fiók lapjára" :::

### <a name="permissions-for-an-aws-linked-account"></a>Egy összekapcsolt AWS-fiók engedélyei

Alapértelmezés szerint az összekapcsolt AWS-fiók engedélyei a fiók létrehozásakor az AWS-összekötő engedélyei alapján vannak beállítva. A tulajdonos az összekötő létrehozója. A hozzáférési szintet az összekapcsolt AWS-fiók **Hozzáférési szint** lapján lehet kezelni. Az összekapcsolt AWS-fiókok nem öröklik az összesített AWS-fiók engedélyeit.

Az összekapcsolt AWS-fiókok mindig öröklik annak a felügyeleti csoportnak az engedélyeit, amelyhez tartoznak.

## <a name="next-steps"></a>További lépések

- Most, hogy beállította és konfigurálta az AWS költség- és használati jelentés integrációját, folytassa [az AWS költségeinek és használatának felügyeletével](aws-integration-manage.md).
- Ha nem ismeri a költségelemzést, lásd a [költségek költségelemzés használatával történő feltérképezését és elemzését](quick-acm-cost-analysis.md) ismertető gyorsútmutatót.
- Ha nem ismeri a költségvetéseket az Azure-ban, lásd az [Azure-költségvetések létrehozását és kezelését](tutorial-acm-create-budgets.md) ismertető szakaszt.
