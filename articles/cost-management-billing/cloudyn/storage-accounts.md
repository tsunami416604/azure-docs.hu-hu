---
title: Storage-fiókok konfigurálása a Cloudyn számára az Azure-ban | Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Storage-fiókokat és az AWS Storage-gyűjtőket a Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: 2a8f3645dcf2987d0c0a204e7e8a86cd6d718e47
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994869"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Storage-fiókok konfigurálása a Cloudyn számára

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

A Cloudyn-jelentéseket a Cloudyn-portálon, az Azure Storage-ban vagy az AWS Storage-gyűjtőn is mentheti. A jelentések a Cloudyn portálra való mentése díjmentes. Azonban a jelentések a felhőalapú szolgáltató tárterületére való mentése nem kötelező, és további költségekkel jár. Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure Storage-fiókokat és a Amazon Web Services (AWS) tárolókat a jelentések tárolásához.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy Azure Storage-fiókkal vagy egy Amazon Storage-gyűjtővel.

Ha nem rendelkezik Azure Storage-fiókkal, létre kell hoznia egyet. További információ az Azure Storage-fiókok létrehozásáról: [Storage-fiók létrehozása](../../storage/common/storage-account-create.md).

Ha nem rendelkezik AWS-beli egyszerű tárolási szolgáltatással (S3), létre kell hoznia egyet. Az S3 gyűjtő létrehozásával kapcsolatos további információkért lásd: [create a Bucket](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Az Azure Storage-fiók konfigurálása

Az Azure Storage Cloudyn általi használatára való konfigurálása egyszerű. Gyűjtsön adatokat a Storage-fiókról, és másolja őket a Cloudyn-portálon.

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. Kattintson a **minden szolgáltatás**elemre, válassza a **Storage-fiókok**lehetőséget, görgessen a használni kívánt Storage-fiókhoz, majd válassza ki a fiókot.
3. A Storage-fiók lap **Beállítások**területén kattintson a **hozzáférési kulcsok**elemre.
4. Másolja a **Storage-fiók nevét** és a **kapcsolatok karakterláncát** a key1 területen.  
   ![másolja a Storage-fiók nevét és a kapcsolatok karakterláncát](./media/storage-accounts/azure-storage-access-keys.png)  
5. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com webhelyre, és jelentkezzen be.
6. Kattintson a fogaskerék szimbólumra, majd válassza a **jelentések tároló kezelése**lehetőséget.
7. Kattintson az **új hozzáadása +** elemre, és győződjön meg arról, hogy Microsoft Azure van kiválasztva. Illessze be az Azure Storage-fiók nevét a **név** területre. Illessze be a kapcsolódó területre a **kapcsolatok karakterláncát** . Adja meg a tároló nevét, majd kattintson a **Mentés**gombra.  
   ![illessze be az Azure Storage-fiók nevét és a kapcsolatok karakterláncát az új jelentés-tároló hozzáadása mezőbe](./media/storage-accounts/azure-cloudyn-storage.png)

   Megjelenik az új Azure Report Storage-bejegyzés a Storage-fiók listában.  
    ![Új Azure Report Storage-bejegyzés a listában](./media/storage-accounts/azure-storage-entry.png)


Mostantól mentheti a jelentéseket az Azure Storage szolgáltatásba. A jelentésekben kattintson a **műveletek** , majd az **ütemezett jelentés**elemre. Nevezze el a jelentést, majd adja hozzá a saját URL-címét, vagy használja az automatikusan létrehozott URL-címet. Válassza a **Mentés a tárolóba** lehetőséget, majd válassza ki a Storage-fiókot. Adjon meg egy előtagot, amely hozzáfűzve lesz a jelentési fájl nevéhez. Válassza a CSV vagy JSON fájlformátum lehetőséget, majd mentse a jelentést.

## <a name="configure-an-aws-storage-bucket"></a>AWS Storage-gyűjtő konfigurálása

A Cloudyn meglévő AWS hitelesítő adatokat használ: felhasználó vagy szerepkör, hogy mentse a jelentéseket a gyűjtőbe. A hozzáférés teszteléséhez a Cloudyn egy kisméretű szövegfájlt próbál menteni a gyűjtőbe a _Check-Bucket-Permission. txt_fájl nevével.

Adja meg a Cloudyn szerepkört vagy felhasználót a PutObject engedéllyel a gyűjtőhöz. Ezután használjon egy meglévő gyűjtőt, vagy hozzon létre egy újat a jelentések mentéséhez. Végül döntse el, hogyan kezelje a tárolási osztályt, hogyan állítson be életciklus-szabályokat, vagy távolítsa el a szükségtelen fájlokat.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Engedélyek kiosztása az AWS-felhasználóhoz vagy-szerepkörhöz

Új szabályzat létrehozásakor meg kell adnia a jelentések S3 gyűjtőbe való mentéséhez szükséges engedélyeket.

1. Jelentkezzen be az AWS-konzolra, és válassza a **szolgáltatások**lehetőséget.
2. Válassza a **iam** lehetőséget a szolgáltatások listájából.
3. A konzol bal oldalán válassza a **házirendek** elemet, majd kattintson a **házirend létrehozása**elemre.
4. Kattintson a **JSON** fülre.
5. A következő szabályzat lehetővé teszi, hogy egy jelentést egy S3 gyűjtőnek mentsen. Másolja és illessze be a következő házirend-példát a **JSON** -lapra. cserélje le &lt;bucketname&gt; a gyűjtő nevére.

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. Kattintson a **házirend áttekintése**elemre.  
    ![AWS JSON-szabályzat, amely példákat mutat be](./media/storage-accounts/aws-policy.png)  
7. A házirend áttekintése lapon adja meg a szabályzat nevét. Például: _CloudynSaveReport2S3_.
8. Kattintson a **házirend létrehozása**elemre.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>A szabályzat csatolása egy Cloudyn szerepkörhöz vagy felhasználóhoz a fiókban

Az új szabályzat csatolásához nyissa meg az AWS-konzolt, és szerkessze a Cloudyn szerepkört vagy felhasználót.

1. Jelentkezzen be az AWS-konzolra, és válassza a **szolgáltatások**lehetőséget, majd válassza a **iam** lehetőséget a szolgáltatások listájából.
2. Válassza ki a **szerepköröket** vagy a **felhasználókat** a konzol bal oldalán.

**Szerepkörök esetén:**

  1. Kattintson a Cloudyn-szerepkör nevére.
  2. Az **engedélyek** lapon kattintson a **házirend csatolása**elemre.
  3. Keresse meg a létrehozott szabályzatot, és válassza ki, majd kattintson a **házirend csatolása**elemre.
    ![például a Cloudyn-szerepkörhöz csatolt szabályzatot](./media/storage-accounts/aws-attach-policy-role.png)

**Felhasználók számára:**

1. Válassza ki a Cloudyn-felhasználót.
2. Az **engedélyek** lapon kattintson az **engedélyek hozzáadása**lehetőségre.
3. A **jogosultság megadása** szakaszban válassza a **meglévő szabályzatok közvetlen csatolása**lehetőséget.
4. Keresse meg a létrehozott szabályzatot, és válassza ki, majd kattintson a **Tovább gombra: Áttekintés**.
5. Az engedélyek hozzáadása szerepkör neve lapon kattintson az **engedélyek hozzáadása**lehetőségre.  
    ![például a Cloudyn-felhasználóhoz csatolt szabályzatot](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Opcionális: engedély beállítása a gyűjtői szabályzattal

Azt is beállíthatja, hogy az S3 gyűjtőn jelentéseket hozzon létre egy gyűjtő házirend használatával. A klasszikus S3 nézetben:

1. Hozzon létre vagy válasszon ki egy meglévő gyűjtőt.
2. Válassza az **engedélyek** fület, majd kattintson a **gyűjtő házirend**elemre.
3. Másolja és illessze be a következő házirend-mintát. Cserélje le &lt;Bucket\_Name&gt; és &lt;Cloudyn\_elvét&gt; a gyűjtőre. Cserélje le a Cloudyn által használt szerepkör vagy felhasználó ARN értékét.

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. A gyűjtő házirend-szerkesztőjében kattintson a **Mentés**gombra.

### <a name="add-aws-report-storage-to-cloudyn"></a>AWS-jelentés tárolójának hozzáadása a Cloudyn-hez

1. Nyissa meg a Cloudyn portált az Azure Portalról, vagy lépjen a https://azure.cloudyn.com webhelyre, és jelentkezzen be.
2. Kattintson a fogaskerék szimbólumra, majd válassza a **jelentések tároló kezelése**lehetőséget.
3. Kattintson az **új hozzáadása +** elemre, és ellenőrizze, hogy az AWS ki van-e választva.
4. Válasszon ki egy fiókot és egy tároló-gyűjtőt. Az AWS Storage-gyűjtő neve automatikusan kitöltődik.  
    ![Példa az új jelentéskészítési tároló hozzáadása mezőre](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Kattintson a **Mentés** , majd **az OK**gombra.

    Az új AWS-jelentés tárolási bejegyzése megjelenik a Storage-fiók listában.  
    ![Új AWS jelentés Storage-bejegyzés megjelenítése a Storage-fiókok listájában](./media/storage-accounts/aws-storage-entry.png)


Mostantól mentheti a jelentéseket az Azure Storage szolgáltatásba. A jelentésekben kattintson a **műveletek** , majd az **ütemezett jelentés**elemre. Nevezze el a jelentést, majd adja hozzá a saját URL-címét, vagy használja az automatikusan létrehozott URL-címet. Válassza a **Mentés a tárolóba** lehetőséget, majd válassza ki a Storage-fiókot. Adjon meg egy előtagot, amely hozzáfűzve lesz a jelentési fájl nevéhez. Válassza a CSV vagy JSON fájlformátum lehetőséget, majd mentse a jelentést.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Cloudyn-jelentések ismertetése](understanding-cost-reports.md) című témakört, amely az Cloudyn-jelentések alapvető szerkezetét és funkcióit ismerteti.
