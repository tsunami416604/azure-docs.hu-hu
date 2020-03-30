---
title: Jar-függőségek kezelése – Azure HDInsight
description: Ez a cikk a HDInsight-alkalmazások Java Archive (JAR) függőségei kezelésével kapcsolatos gyakorlati tanácsokat ismerteti.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135732"
---
# <a name="jar-dependency-management-best-practices"></a>JAR-függőségkezelés – gyakorlati tanácsok

A HDInsight-fürtökre telepített összetevők külső gyártótól származó könyvtáraktól függenek. Általában, egy adott változata a közös modulok, mint a Guava hivatkozik ezek a beépített alkatrészek. Amikor egy alkalmazás a függőségek, ez ütközést okozhat a különböző verziói ugyanazon modul. Ha az osztályelérési úton először hivatkozott összetevő-verzió, a beépített összetevők kivételeket eredményezhetnek a verzióink inkompatibilitása miatt. Ha azonban a beépített összetevők először a classpath-ba injektálják `NoSuchMethod`a függőségeiket, az alkalmazás hibákat okozhat, például .

A verzióütközések elkerülése érdekében fontolja meg az alkalmazásfüggőségek árnyékolását.

## <a name="what-does-package-shading-mean"></a>Mit jelent a csomag árnyékolása?
Az árnyékolás lehetővé teszi a függőségek felvételét és átnevezését. Áthelyezi az osztályokat, és átírja az érintett bytecode és erőforrások at, hogy hozzon létre egy privát másolatot a függőségek.

## <a name="how-to-shade-a-package"></a>Hogyan kell árnyékolni egy csomagot?

### <a name="use-uber-jar"></a>Uber-jar használata
Az Uber-jar egy egyetlen jar fájl, amely tartalmazza az alkalmazás jar és a függőségek. Az Uber-jar függőségei alapértelmezés szerint nem árnyékoltak. Bizonyos esetekben ez verzióütközést okozhat, ha más összetevők vagy alkalmazások a könyvtárak egy másik verziójára hivatkoznak. Ennek elkerülése érdekében létrehozhat egy Uber-Jar fájlt néhány (vagy az összes) a függőségek árnyékolt.

### <a name="shade-package-using-maven"></a>Shade csomag maven használatával
A Maven java és Scala nyelven is készíthet alkalmazásokat. Maven-shade-plugin segítségével hozzon létre egy árnyékos uber-jar könnyen.

Az alábbi példa `pom.xml` egy fájlt mutat be, amelyet úgy frissítettek, hogy árnyékot vessen a maven-shade-plugin használatával.  Az XML-szakasz `<relocation>…</relocation>` a `com.google.guava` megfelelő `com.google.shaded.guava` JAR-fájlbejegyzések áthelyezésével és az érintett bytecode újraírásával áthelyezi az osztályokat a csomagról a csomagra.

A `pom.xml`módosítása után `mvn package` hajthatja végre az árnyékolt über-jar.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Shade csomag sbt használatával
SBT is egy épít szerszám részére Scala és Jávai. SBT nincs árnyék plugin, mint a maven-shade-plugin. A csomagok `build.sbt` árnyékolásával módosíthatja a fájlt. 

Például az `com.google.guava`árnyékoláshoz hozzáadhatja az `build.sbt` alábbi parancsot a fájlhoz:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Ezután `sbt clean` futtathatja `sbt assembly` és létrehozhatja az árnyékolt jar fájlt. 

## <a name="next-steps"></a>További lépések

* [A HDInsight IntelliJ eszközök használata](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Hozzon létre egy Scala Maven alkalmazást a Spark in IntelliJ-ben](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
