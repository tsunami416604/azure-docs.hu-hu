Az Azure Resource Manager határozhatja meg a sablon telepítésekor használni kívánt értékeket a paraméterek. A sablon tartalmaz egy `parameters` a paraméterek értékeit tartalmazó szakasz. Minden paraméter értéke szolgál a sablonhoz adja meg a telepíteni kívánt erőforrásokat.

> [!NOTE]
> Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. Paramétereit csak értékek, amelyek eltérnek a központilag telepíteni projektre vagy a környezet telepítéséhez alapján.

Ha paraméterek meghatározása:

* Adja meg az engedélyezett értékeket, amely a felhasználó képes biztosítani a telepítés során, a **Storageaccount_accounttype** mező.

* Ha nincs központi telepítése során biztosított hozzá paraméter alapértelmezett értékeket, használja a **defaultValue** mező. 
