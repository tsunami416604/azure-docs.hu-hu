---
title: Adatok importálása egy fájlból az Azure Machine Learning studióhoz |} A Microsoft Docs
description: Útmutató a betanítási adatok fájlok feltöltéséhez a merevlemez-meghajtóról az Azure Machine Learning Studióban. Ez létrehoz egy adatkészlet-modul a munkaterületen.
keywords: adatok, az adatok formátuma, adattípusok, adatforrások, betanítási adatok importálása
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: c0dd9e90-23c4-4f64-8b8f-489ad79f047b
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 61c6f6a4dbd5b35482b540a9d073502523696d78
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824105"
---
# <a name="import-training-data-from-a-file-on-your-hard-drive-into-machine-learning-studio"></a>Betanítási adatok importálása egy fájlból a merevlemezen, Machine Learning studióba

Ismerje meg, hogyan tölthet fel egy adatfájlt a merevlemez-meghajtóról a betanítási adatok az Azure Machine Learning Studióban adatokként. Importálja a fájlt, van egy használatra kész adatkészlet modul munkaterületét.

## <a name="steps-to-import-data-from-a-local-file"></a>Adatokat importálhat egy helyi fájlból lépései
Adatokat importálhat a helyi merevlemezen, tegye a következőket:

1. Kattintson a **+ új** a Machine Learning Studio ablakának alján.
2. Válassza ki **ADATKÉSZLET** és **helyi FÁJLBÓL**.
3. Az a **töltse fel egy új adatkészlet** párbeszédpanelen tallózással keresse meg a feltölteni kívánt fájlt
4. Adjon meg egy nevet, az adattípus azonosításához, és igény szerint adjon meg egy leírást. Leírását javasolt – lehetővé teszi bármely jellemzőinek a jövőben az adatok használatakor vegye figyelembe az adatok rögzítéséhez.
5. A jelölőnégyzet **az új verziója, amelyet egy meglévő adatkészlet** lehetővé teszi, hogy egy meglévő adatkészlet új adatokkal frissülnek. Kattintson a jelölőnégyzetre, és írja be egy meglévő adatkészlet nevét.

![Töltse fel egy új adatkészlet](./media/import-data-from-local-file/upload-dataset.png)

Feltöltésekor megjelenik egy üzenet, hogy a fájl feltöltése folyamatban van. Feltöltés ideje az adatok méretétől és a szolgáltatás a kapcsolat sebességétől függ. Ha tudja, hogy a fájl egy hosszú időt vesz igénybe, további tevékenység következik, Machine Learning Studio belül megteheti, várakozás közben. Azonban a böngésző bezárásával hatására az adatok feltöltése sikertelen.

## <a name="dataset-module-is-ready-for-use"></a>Adatkészlet modul készen áll a használatra
Az adatok feltöltését követően egy adatkészlet modulban tárolja, és bármely a kísérletvászonra a munkaterületén érhető el.

Kísérlet szerkesztésekor találja a korábban létrehozott adatkészletek a **saját adatkészletek** alatt a **mentett adatkészletek** a modulpaletta listájában. Húzza át, és dobja el az adatkészletet a kísérlet vászonra, ha meg szeretné használni az adatkészlet további elemzés és gépi tanulás.
