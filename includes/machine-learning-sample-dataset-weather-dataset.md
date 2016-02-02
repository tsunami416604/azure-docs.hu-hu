每小時起降土地天候觀測值 (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">201304 的資料合併至 201310</a>)。<p></p>天氣資料涵蓋從機場天候觀測站，涵蓋期間年 4 月到 10 月 2013年。 之前上傳到 Azure ML Studio 中，資料集處理，如下所示:<ul><li>天候觀測站識別碼已對應至相對應的機場識別碼</li><li>過濾掉與 70 個最繁忙的機場沒有關聯的天候觀測站</li><li>日期資料行已分割為個別的年、 月和日資料行</li><li>已選取下列資料行: AirportID、 年份、 月份、 日期、 時間、 時區、 SkyCondition、 可見性、 WeatherType、 DryBulbFarenheit、 DryBulbCelsius、 WetBulbFarenheit、 WetBulbCelsius、 DewPointFarenheit、 DewPointCelsius、 RelativeHumidity、 WindSpeed、 WindDirection、 ValueForWindCharacter、 StationPressure、 PressureTendency、 PressureChange、 SeaLevelPressure、 RecordType、 HourlyPrecip、 Altimeter</li></ul>




