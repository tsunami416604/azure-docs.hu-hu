---
title: Amazon Web Services-fiók összekötése az Azure-beli Cloudyn | Microsoft Docs
description: Amazon Web Services fiók összekapcsolásával megtekintheti a Cloudyn-jelentésekben szereplő költségeket és használati adatokat.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: b64d54df43b27abb51210995f2426e23690fa2d3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994882"
---
# <a name="connect-an-amazon-web-services-account"></a>Amazon Web Services-fiók összekötése

Két lehetőség közül választhat a Amazon Web Services (AWS) fiók Cloudyn való összekapcsolásához. Egy IAM szerepkörrel vagy egy csak olvasható, csak olvasási jogosultsággal rendelkező felhasználói fiókkal csatlakozhat. A IAM szerepkör használata ajánlott, mert lehetővé teszi a hozzáférés delegálását a megbízható entitásokhoz meghatározott engedélyekkel. A IAM szerepkörhöz nem szükséges a hosszú távú hozzáférési kulcsok megosztása. Az AWS-fiók Cloudyn való összekapcsolását követően a Cloudyn-jelentésekben elérhető a Cost és a használati adatok. Ez a dokumentum mindkét lehetőséget végigvezeti.

Az AWS IAM-identitásokkal kapcsolatos további információkért lásd: [identitások (felhasználók, csoportok és szerepkörök)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html).

Emellett engedélyezheti az AWS részletes számlázási jelentéseit, és tárolhatja az adatokat egy AWS egyszerű Storage szolgáltatásbeli (S3) gyűjtőben. A részletes számlázási jelentések óradíjat tartalmaznak a címke-és erőforrásadatok alapján. A jelentések tárolása lehetővé teszi, hogy a Cloudyn beolvassa őket a gyűjtőből, és megjeleníti a jelentésekben szereplő információkat.


## <a name="aws-role-based-access"></a>AWS szerepköralapú hozzáférés

A következő szakasz végigvezeti egy írásvédett IAM-szerepkör létrehozásán, hogy hozzáférést biztosítson a Cloudyn.

### <a name="get-your-cloudyn-account-external-id"></a>Cloudyn-fiók külső AZONOSÍTÓjának beolvasása

Az első lépés a Cloudyn-portál egyedi kapcsolódási jelszavának beolvasása. Az AWS-ben **külső azonosítóként**használatos.

1. Nyissa meg a Cloudyn-portált a Azure Portal, vagy navigáljon a [https://azure.cloudyn.com ra](https://azure.cloudyn.com) , és jelentkezzen be.
2. Kattintson a fogaskerék jelre, majd válassza a **Cloud accounts (Felhőbeli fiókok**) lehetőséget.
3. A fiókok kezelése lapon válassza az **AWS-fiókok** fület, majd kattintson az **új + hozzáadása**lehetőségre.
4. Az **AWS-fiók hozzáadása** párbeszédpanelen másolja át a **külső azonosítót** , és mentse az AWS szerepkör-létrehozási lépések értékét a következő szakaszban. A külső azonosító egyedi a fiókjában. A következő képen a külső azonosító a _contoso_ , amelyet egy szám követ. Az azonosító eltér.  
    ![külső azonosítót az AWS-fiók hozzáadása mezőbe](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>Az AWS csak olvasási szerepkörön alapuló hozzáférés hozzáadása

1. Jelentkezzen be az AWS-konzolra https://console.aws.amazon.com/iam/home és válassza a **szerepkörök**lehetőséget.
2. Kattintson a **szerepkör létrehozása** lehetőségre, majd válasszon **egy másik AWS-fiókot**.
3. A **fiók azonosítója** mezőben illessze be a `432263259397`. Ez a fiókazonosító az AWS által a Cloudyn szolgáltatáshoz hozzárendelt Cloudyn adatgyűjtő-fiók. Használja a pontosan megjelenő fiók AZONOSÍTÓját.
4. A **Beállítások**mellett válassza a **külső azonosító megkövetelése**lehetőséget. Illessze be a korábban a Cloudyn **külső azonosító** mezőjéből másolt egyedi értéket. Ezután kattintson a **Tovább gombra: engedélyek**.  
    ![illessze be a külső azonosítót a Cloudyn elemre a szerepkör létrehozása oldalon](./media/connect-aws-account/create-role01.png)
5. Az **engedélyek csatolása**területen a **házirend típusának** szűrése mezőbe írja be a `ReadOnlyAccess`kifejezést, válassza a **ReadOnlyAccess**, majd a **Tovább: felülvizsgálat**lehetőséget.  
    ![válassza a csak olvasási hozzáférés elemet a házirendek nevei listájában](./media/connect-aws-account/readonlyaccess.png)
6. Az Áttekintés lapon győződjön meg arról, hogy a beállítások helyesek, és adjon meg egy **szerepkör-nevet**. Például: *Azure-Cost-kez*. Adja meg a **szerepkör leírását**. Például a _Cloudyn szerepkör-hozzárendelését_, majd kattintson a **szerepkör létrehozása**lehetőségre.
7. A **szerepkörök** listájában kattintson a létrehozott szerepkörre, és másolja az **ARN (szerepkör** ) értéket az összefoglalás lapról. A Cloudyn-ben a konfiguráció regisztrálása után később használja az ARN (Amazon-erőforrás neve) szerepkört.  
    ![másolja az ARN szerepkört az összefoglalás lapról](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cloudyn"></a>Az AWS IAM szerepkör-hozzáférés konfigurálása a Cloudyn-ben

1. Nyissa meg a Cloudyn-portált a Azure Portal, vagy navigáljon a https://azure.cloudyn.com/ ra, és jelentkezzen be.
2. Kattintson a fogaskerék jelre, majd válassza a **Cloud accounts (Felhőbeli fiókok**) lehetőséget.
3. A fiókok kezelése lapon válassza az **AWS-fiókok** fület, majd kattintson az **új + hozzáadása**lehetőségre.
4. A **fiók neve**mezőben adja meg a fiók nevét.
5. A **hozzáférés típusa**mellett válassza a **iam szerepkör**lehetőséget.
6. Illessze be a korábban másolt értéket a **role ARN** mezőbe, majd kattintson a **Save (Mentés**) gombra.  
    ![illessze be az ARN szerepkört az AWS-fiók hozzáadása mezőbe](./media/connect-aws-account/add-aws-account-box.png)


Az AWS-fiók megjelenik a fiókok listájában. A felsorolt **tulajdonosi azonosító** megfelel a szerepkör ARN értékének. A **fiók állapotának** zöld pipa szimbólummal kell rendelkeznie, amely azt jelzi, hogy a Cloudyn HOZZÁFÉRHET az AWS-fiókhoz. Amíg nem engedélyezi a részletes AWS-számlázást, a konszolidációs állapot **önállóként**jelenik meg.

![Az AWS-fiók állapota megjelenik a fiókok kezelése lapon](./media/connect-aws-account/aws-account-status01.png)

A Cloudyn megkezdi az adatok gyűjtését és a jelentések feltöltését. Ezután [engedélyezze a részletes AWS-számlázást](#enable-detailed-aws-billing).


## <a name="aws-user-based-access"></a>AWS-alapú felhasználói hozzáférés

A következő szakasz végigvezeti a csak olvasási jogosultsággal rendelkező felhasználó létrehozásán, hogy hozzáférést biztosítson a Cloudyn.

### <a name="add-aws-read-only-user-based-access"></a>AWS írásvédett, felhasználói alapú hozzáférés hozzáadása

1. Jelentkezzen be az AWS-konzolra https://console.aws.amazon.com/iam/home és válassza a **felhasználók**lehetőséget.
2. Kattintson a **felhasználó hozzáadása**elemre.
3. A **Felhasználónév** mezőbe írja be a felhasználónevet.
4. A **hozzáférés típusa**beállításnál válassza a **programozott hozzáférés** lehetőséget, majd kattintson a **Tovább gombra: engedélyek**.  
    ![adjon meg egy felhasználónevet a felhasználó hozzáadása lapon](./media/connect-aws-account/add-user01.png)
5. Az engedélyek beállításnál válassza a **meglévő szabályzatok közvetlen csatolása**lehetőséget.
6. Az **engedélyek csatolása**területen a **házirend típusának** szűrése mezőbe írja be a `ReadOnlyAccess`kifejezést, válassza a **ReadOnlyAccess**lehetőséget, majd kattintson a **Tovább gombra: Áttekintés**.  
    ![válassza a ReadOnlyAccess lehetőséget a felhasználó engedélyeinek beállításához](./media/connect-aws-account/set-permission-for-user.png)
7. Az Áttekintés lapon győződjön meg arról, hogy helyesek a megfelelő beállítások, majd kattintson a **felhasználó létrehozása**elemre.
8. A Complete (teljes) lapon megjelenik a hozzáférési kulcs azonosítója és a titkos elérési kulcs. Ezekkel az információkkal konfigurálhatja a regisztrációt a Cloudyn-ben.
9. Kattintson a **download. csv** fájlra, és mentse a hitelesítő adatokat tartalmazó. csv fájlt egy biztonságos helyre.  
    ![a hitelesítő adatok mentéséhez kattintson a Download. csv fájlra](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cloudyn"></a>AWS IAM-alapú felhasználói hozzáférés konfigurálása a Cloudyn-ben

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com/ webhelyre, és jelentkezzen be.
2. Kattintson a fogaskerék jelre, majd válassza a **Cloud accounts (Felhőbeli fiókok**) lehetőséget.
3. A fiókok kezelése lapon válassza az **AWS-fiókok** fület, majd kattintson az **új + hozzáadása**lehetőségre.
4. A **fiók neve**mezőbe írja be a fiók nevét.
5. A **hozzáférés típusa**mellett válassza a **iam felhasználó**elemet.
6. A **hozzáférési kulcsban**illessze be a **hozzáférési kulcs azonosítójának** értékét a hitelesítő adatok. csv fájlból.
7. A **titkos kulcs**mezőben illessze be a **titkos elérési kulcs** értékét a hitelesítő adatok. csv fájlból, majd kattintson a **Mentés**gombra.  

Az AWS-fiók megjelenik a fiókok listájában. A **fiók állapotának** zöld pipa jellel kell rendelkeznie.

A Cloudyn megkezdi az adatok gyűjtését és a jelentések feltöltését. Ezután [engedélyezze a részletes AWS-számlázást](#enable-detailed-aws-billing).

## <a name="enable-detailed-aws-billing"></a>Részletes AWS-számlázás engedélyezése

Az AWS-szerepkört az alábbi lépésekkel veheti igénybe: ARN. Az ARN szerepkör használatával olvasási engedélyeket adhat egy számlázási gyűjtőnek.

1. Jelentkezzen be az AWS-konzolra https://console.aws.amazon.com és válassza a **szolgáltatások**lehetőséget.
2. A szolgáltatás keresési mezőjében írja be a következőt: *iam*, és válassza ezt a lehetőséget.
3. Válassza ki a **szerepkörök** elemet a bal oldali menüben.
4. A szerepkörök listájában válassza ki a Cloudyn-hozzáféréshez létrehozott szerepkört.
5. A Szerepkörök összegzése lapon kattintson ide az **ARN szerepkör**másolásához. Az ARN szerepkört később is megtarthatja a későbbi lépésekhez.

### <a name="create-an-s3-bucket"></a>S3 gyűjtő létrehozása

Hozzon létre egy S3 gyűjtőt a részletes számlázási adatok tárolásához.

1. Jelentkezzen be az AWS-konzolra https://console.aws.amazon.com és válassza a **szolgáltatások**lehetőséget.
2. A szolgáltatás keresési mezőjébe írja be az *S3*kifejezést, majd válassza az **S3**elemet.
3. Az Amazon S3 lapon kattintson a **create Bucket (gyűjtő létrehozása**) elemre.
4. A gyűjtő létrehozása varázslóban válassza ki a gyűjtő nevét és a régiót, majd kattintson a **tovább**gombra.  
    ![példa a Create Bucket (gyűjtő létrehozása) lapra](./media/connect-aws-account/create-bucket.png)
5. A **készlet tulajdonságai** lapon tartsa meg az alapértelmezett értékeket, majd kattintson a **tovább**gombra.
6. Az Áttekintés lapon kattintson a **gyűjtő létrehozása**elemre. Megjelenik a gyűjtő lista.
7. Kattintson a létrehozott gyűjtőre, és válassza az **engedélyek** fület, majd válassza a **gyűjtő házirend**elemet. Megnyílik a gyűjtő házirend-szerkesztője.
8. Másolja a következő JSON-példát, és illessze be a gyűjtő házirend-szerkesztőjébe.
   - Cserélje le a `<BillingBucketName>`t az S3 gyűjtő nevére.
   - Cserélje le a `<ReadOnlyUserOrRole>`t arra a szerepkörre vagy felhasználói ARN-ra, amelyet korábban már másolt.

   ```json
   {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
   }
   ```

9. Kattintson a **Mentés** gombra.  
    ![kattintson a Mentés gombra a gyűjtő házirend-szerkesztőjében](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>AWS számlázási jelentések engedélyezése

Az S3-gyűjtő létrehozása és konfigurálása után navigáljon a [számlázási beállítások](https://console.aws.amazon.com/billing/home?#/preference) pontra az AWS-konzolon.

1. A beállítások lapon válassza a **Számlázási jelentések fogadása**lehetőséget.
2. A **Számlázási jelentések fogadása**területen adja meg a létrehozott gyűjtő nevét, majd kattintson az **ellenőrzés**gombra.  
3. Válassza ki mind a négy jelentés részletességi beállításait, majd kattintson a **Beállítások mentése**lehetőségre.  
    ![a jelentések engedélyezéséhez válassza a részletesség lehetőséget](./media/connect-aws-account/enable-reports.png)

A Cloudyn részletes számlázási adatokat kér le az S3 gyűjtőtől, és a részletes számlázás engedélyezése után feltölti a jelentéseket. Akár 24 óráig is eltarthat, amíg a részletes számlázási információk megjelennek a Cloudyn-konzolon. Ha részletes számlázási információk állnak rendelkezésre, a fiók konszolidációs állapota **összevonva**jelenik meg. A fiók állapota **befejezettként**jelenik meg.

![Az AWS-fiókok lapon megjelenő konszolidációs állapot](./media/connect-aws-account/consolidated-status.png)

Néhány optimalizálási jelentéshez szükség lehet néhány napos adatra, hogy a pontos ajánlásokhoz megfelelő adatmennyiséget kapjon.

## <a name="next-steps"></a>Következő lépések

- Ha többet szeretne megtudni a Cloudyn-ről, folytassa a használati és Cloudyn kapcsolatos oktatóanyag [áttekintésével](tutorial-review-usage.md) .
