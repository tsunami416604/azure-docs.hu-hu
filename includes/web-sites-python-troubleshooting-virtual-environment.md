A telepítési parancsfájl kihagyja a virtuális környezet létrehozását az Azure-on, ha úgy észleli, hogy már létezik kompatibilis virtuális környezet.  Ez jelentősen felgyorsíthatja a telepítést.  A pip kihagyja a már telepített csomagok telepítését.

Bizonyos esetekben érdemes lehet kényszeríteni a létező virtuális környezet törlését.  Ezt akkor célszerű megtenni, ha a tárház részeként szeretne használni virtuális környezetet.  Akkor is érdemes lehet a törlés, ha el szeretne távolítani egyes csomagokat, vagy tesztelni szeretné a requirements.txt fájl módosításait.

A létező virtuális környezet több módon is kezelhető az Azure-ban:

### 1. lehetőség: FTP használata

Csatlakozzon a kiszolgálóhoz FTP-ügyféllel – ekkor törölheti az env mappát.  Vegye figyelembe, hogy egyes FTP-ügyfelek (például a webböngészők) csak olvasható hozzáférést biztosítanak, így nem teszik lehetővé a mappák törlését. Ügyeljen arra, hogy olyan FTP-ügyfelet használjon, amely biztosítja ezt a képességet.  Az FTP-állomás neve és a felhasználó az [Azure Portalon](https://portal.azure.com) a webalkalmazása panelén jelenik meg.

### 2. lehetőség: Futtatókörnyezet-váltás

Ez egy olyan alternatíva, amely azt használja ki, hogy a telepítési parancsfájl törli az env mappát, ha az nem egyezik meg a Python kívánt verziójával.  Ez a módszer így törli a létező környezetet, és létrehoz egy újat.

1. Váltson át a Python egy másik verziójára (a runtime.txt fájlon keresztül, vagy az Azure Portal **Alkalmazásbeállítások** panelén)
1. továbbítson módosításokat a gittel (hagyja figyelmen kívül az esetleges pip-telepítési hibákat)
1. Váltson vissza a Python eredeti verziójára
1. továbbítson újabb módosításokat a gittel

### 3. lehetőség: A telepítési parancsfájl testreszabása

Ha testre szabta a telepítési parancsfájlt, módosíthatja a deploy.cmd fájl kódját, hogy az törölje az env mappát.


<!--HONumber=Sep16_HO4-->


