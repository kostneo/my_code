# my_code
my_code

echo("<center><input type=submit name=viewed value='Самые просматриваемые достопримечательности'></center><br><br>");

echo("<center><input type=submit name=popular value='Самые популярные достопримечательности'></center><br><br>");

echo("<center><input type=submit name=many_rooms value='Достопримечательность, рядом с кторой наибольшее кол-во номеров'></center><br><br>");

echo("<center><input type=submit name=many value='Достопримечательность, рядом с кторой наибольшее кол-во койко-мест'></center><br><br>");

echo("<center><input type=submit name=few value='Популярная достопремичательность, рядом с кторой наименьшее кол-во койко-мест'></center><br><br>");



//echo("<center><input type=submit name=change value='Изменить пользователя'></center><br><br><br>");
//echo("<center><input type=submit name=delete value='Удалить пользователя'></center><br><br><br>");
//echo("<center><input type=submit name=view value='Просмотреть пользователя'></center><br><br><br>");
//echo("<center><input type=submit name=servers value='Работа с Серверами'></center><br><br><br>");
//echo("<center><input type=submit name=bases value='Работа с Базами'></center><br><br><br>");
//echo("<center><input type=submit name=macs value='Работа с Mac адресами'><br><br><br></center>");
//echo("<center><input type=submit name=data value='Работа с данными Корпорации'></center>");


echo("<td valign=1 width=25% align=center>");
echo ("</form>");


#================================= Условие нажатия на кнопку ======================================
 if (!empty($_GET["load"])) 
 {
	 $index_string = 0;
	 $index_fields = 0;
	 $zapros = "";
	 $list_fields = "";
	 
	 if (isSet($_GET['tables'])) 
		{
			foreach ($_GET['tables'] as $table)
			{
				$adress='B:/Mriya/'.$table.'.csv';
				echo("<br>$adress<br>");
				$table_name =  $table;
			}
		}
	 
	 echo("<br>Загрузка... $table<br>");
			
	
	 $stream_text = fopen($adress, 'r');
	 
	 $find_table = false;
	 $find_table = find_table($table_name);
	 if ($find_table == true)
	{
		echo ("<br>Данная таблица уже загружена!!!<br>");
	}
	else
	{
		if ($stream_text)
		{

			while (!feof($stream_text))
			{
				$index_string++;
				$string_text = fgets($stream_text, 999);
				$string_text = str_replace('"',"", $string_text);
				//echo("<br>$string_text");
				$fields = explode(';' , $string_text);
				if ($index_string == 1)
				{	
							
						$zapros = "";
						$list_fields = "";
						$list_fields_no_types = "";
						$index_field = 0;
						foreach ($fields as $field)
						{
							$is_id = strripos($fields[$index_field], "id");
							
							
							switch (trim($fields[$index_field]))
							{
								case "id": $type[$index_field] = "CHAR(50) NOT NULL PRIMARY KEY";
										break;
								case "count": $type[$index_field] = "INT";
										break;
								case "occupancy": $type[$index_field] = "INT";
										break;
								case "distance_to_center": $type[$index_field] = "INT";
										break;
								case "popularity": $type[$index_field] = "INT";
										break;
								case "serial_number": $type[$index_field] = "INT";
										break;
								case "sort": $type[$index_field] = "INT";
										break;					
								case "distance": $type[$index_field] = "INT";
										break;
								//case "insert_stamp": $type[$index_field] = "DATETIME";
								//		break;
								case "latitude": $type[$index_field] = "FLOAT";
										break;
								case "longitude": $type[$index_field] = "FLOAT";
										break;
								case "is_visible": $type[$index_field] = "BIT";
										break;
								case "insert_stamp": $type[$index_field] = "CHAR(20)";
										break;
								case "name": $type[$index_field] = "CHAR(100)";
										break;		
								default:  $type[$index_field] = "CHAR(50)";
							}
							
							if (($is_id > 0) and ($fields[$index_field]<>"id"))	
							{
								$type[$index_field] = "CHAR(50)";
							/*switch (trim($fields[$index_field]))
								{
									case "place_id": $type[$index_field] = "CHAR(50) FOREIGN KEY (place_id) REFERENCES ref_city_place (id)";
										break;
									case "city_id": $type[$index_field] = "CHAR(50) FOREIGN KEY (city_id) REFERENCES ref_city (id)";
										break;
									default: $type[$index_field] = "CHAR(50)";
								}*/
							}
							//echo ("<br>$type[$index_field] ");
							$list_fields = $list_fields."$fields[$index_field] ".$type[$index_field].", ";
							$list_fields_no_types = $list_fields_no_types."$fields[$index_field]".", ";
							unset ($fields[$index_field]);
							$index_field++;
						}
						$list_fields = erase_coma($list_fields);
						$list_fields_no_types = erase_coma($list_fields_no_types);
						$zapros = "CREATE TABLE $table_name (".$list_fields.")";
						echo ("<br>$zapros<br>");
						run_zapros($zapros);	
				
				}
				else
				{
					$zapros = "";
					$list_temp = "";
					$index_temp = 0;
					
					foreach ($fields as $field)
						{
							$is_char = strripos($type[$index_temp], "HAR");
							if ($is_char > 0) $skobka = "'"; else $skobka = "";
							if ($type[$index_temp] == "BIT") if (trim($fields[$index_temp]) == "True") $fields[$index_temp] = 1; else $fields[$index_temp]=0;
							if ($type[$index_temp] == "FLOAT") $fields[$index_temp] = str_replace (",",".", $fields[$index_temp]);
						
								 
							 
							$list_temp = $list_temp.$skobka."$fields[$index_temp]".$skobka.", ";
							$fields[$index_temp];
							$index_temp++;
						}
					$prdpos = strlen($list_temp)-2;
					$list_temp = substr_replace($list_temp , "", $prdpos, 1 );

					$zapros = "INSERT INTO $table_name (".$list_fields_no_types.") values (".$list_temp.")";
					echo ("<br>$zapros");
					run_zapros($zapros);
				}
			}
		}
	}

	fclose($stream_text);

 }
 
 if (!empty($_GET["clear"])) 
 {
	 $zapros = "DROP TABLE ref_city, city_place_log, hotels_lnk_city, hotels_lnk_city_place, ref_city_place, ref_hotels_rooms";
	 run_zapros($zapros);
 }
 
  if (!empty($_GET["anomaly"])) 
 {	

	$table_name[1] =  'ref_city'; 
	$table_name[2] =  'city_place_log'; 
	$table_name[3] =  'hotels_lnk_city';
	$table_name[4] =  'hotels_lnk_city_place';
	$table_name[5] =  'ref_city_place';
	$table_name[6] =  'ref_hotels_rooms';
	
	for ($j=1;$j<6;$j++) 
	{
		$string_table = 0;
		$name = $table_name[$j];
		$sql = "
		select max(ordinal_position)
		from information_schema.columns where table_name = '$name'
		";
		$sql = mssql_query($sql);
		while($obResults = mssql_fetch_row($sql)) 
		{
			$column_quantity = $obResults[0];
		}
	
		$string_table=1; 
		$sql = "
		select *
		from $name
		";
		$sql = mssql_query($sql);
		while($obResults = mssql_fetch_row($sql)) 
		{
			for ($i=0;$i<$column_quantity;$i++)
			{	
				if ((trim($obResults[$i]) == "unknown") or (trim($obResults[$i]) == "тест") /*or (trim($obResults[$i]) == '0')*/) 
				{
					echo ("<br>В таблице $name в строке $string_table в поле $i содержаться аномальные данные: $obResults[$i]");
				}
			}	
			$string_table++; 
		}
		
	}
 } 
	 
	 
  if (!empty($_GET["popular"])) 
 {
	$sql = "
	select id, serial_number, popularity
	from ref_city_place
	where popularity = (select max(popularity)as maximum
	from ref_city_place)
	 ";
	 $sql = mssql_query($sql);
	while($obResults = mssql_fetch_row($sql)) 
	{
		$popular_place_id = $obResults[0];
		$serial_number = $obResults[1];
		$popularity = $obResults[1];
	}
	echo ("<br>Самое популярное место с серийным номером: $serial_number");
	echo ("<br>Его ID: $popular_place_id");
	echo ("<br>Показатель популярности: $popularity");
 }
 
  if (!empty($_GET["viewed"])) 
 {
	 $index_max=0;
	 $sql = "
	select distinct place_id, count(*) as visit
	from city_place_log
	group by place_id
	order by visit desc
	 ";
	 $sql = mssql_query($sql);
	while($obResults = mssql_fetch_row($sql)) 
	{
		if ($index_max==0) 
		{
			$viewer_place_id = trim($obResults[0]);
			$quantity_visits = $obResults[1];
			$index_max++;
			break;
		}
	}
	
	
	$serial_number = "";
	$serial_number = get_place_number($viewer_place_id);
	
	echo ("<br>Самое просматриваемое место с серийным номером: $serial_number");
	echo ("<br>Его ID: $viewer_place_id");
	echo ("<br>Количество просмотров в логе: $quantity_visits");
	
 }		
 
 
 if (!empty($_GET["many_rooms"])) 
 {
	 $nearby = 10;
	 $max_room = 0;
	 $sql = "
		select hotels_lnk_city_place.place_id, hotels_lnk_city_place.distance, max(ref_hotels_rooms.count) as room
		from hotels_lnk_city_place
		inner join ref_hotels_rooms on (hotels_lnk_city_place.home_id like ref_hotels_rooms.home_id)
		group by  hotels_lnk_city_place.place_id, hotels_lnk_city_place.distance
		order by hotels_lnk_city_place.distance, room DESC
		";
	$sql = mssql_query($sql);
	while($obResults = mssql_fetch_row($sql)) 
	{
		if ($obResults[1]<$nearby)
		{
			if ($obResults[2]>$max_room) 
			{
				$max_room=$obResults[2];
				$place_id = trim($obResults[0]);
			}
			
		}
		else break;
	}
	$serial_number = "";
	$serial_number = get_place_number($place_id);
	
	echo ("<br>Достопримечательность, рядом с которой больше всего номеров:");
	echo ("<br>Серийный номер: $serial_number");
	echo ("<br>ID: $place_id");
	echo ("<br>Количество номеров: $max_room");
	echo ("<br>При условии, что рядом - это дистанция не более:  $nearby");
 }
 
 
 if (!empty($_GET["many"])) 
 {
	 $nearby = 10;
	 $max_bed = 0;
	 $sql = "
		select hotels_lnk_city_place.place_id, hotels_lnk_city_place.distance, max(ref_hotels_rooms.count * ref_hotels_rooms.occupancy) as koyka
		from hotels_lnk_city_place
		inner join ref_hotels_rooms on (hotels_lnk_city_place.home_id like ref_hotels_rooms.home_id)
		group by  hotels_lnk_city_place.place_id, hotels_lnk_city_place.distance
		order by hotels_lnk_city_place.distance, koyka DESC
		";
	$sql = mssql_query($sql);
	while($obResults = mssql_fetch_row($sql)) 
	{
		if ($obResults[1]<$nearby)
		{
			if ($obResults[2]>$max_bed) 
			{
				$max_bed=$obResults[2];
				$place_id = trim($obResults[0]);
			}
			
		}
		else break;
	}
	$serial_number = "";
	$serial_number = get_place_number($place_id);
	
	echo ("<br>Достопримечательность, рядом с которой больше всего койко-мест:");
	echo ("<br>Серийный номер: $serial_number");
	echo ("<br>ID: $place_id");
	echo ("<br>Количество койко-мест: $max_bed");
	echo ("<br>При условии что рядом это дистанция не более:  $nearby");
 }
 
 if (!empty($_GET["few"])) 
 {
	 $nearby = 10;
	 $min_bed = 1000;
	 $known = 100;
	 $place_id = "";
	 $popularity = "";
	 $sql = "
	 select hotels_lnk_city_place.place_id, hotels_lnk_city_place.distance, min(ref_hotels_rooms.count * ref_hotels_rooms.occupancy) as koyka
		from hotels_lnk_city_place
		inner join ref_hotels_rooms on (hotels_lnk_city_place.home_id like ref_hotels_rooms.home_id)
		group by  hotels_lnk_city_place.place_id, hotels_lnk_city_place.distance
		order by hotels_lnk_city_place.distance, koyka
		";
	$sql = mssql_query($sql);
	while($obResults = mssql_fetch_row($sql)) 
	{
		if ($obResults[1]<$nearby)
		{
			if ($obResults[2]<$min_bed) 
			{
				$popularity = get_popularity(trim($obResults[0]));
				if ($popularity>$known)
				{
					$min_bed=$obResults[2];
					$place_id = $obResults[0];
				}
			}
		}
		else break;
	}
	$serial_number = "";
	$serial_number = get_place_number($place_id);
	
	echo ("<br>Популярная достопримечательность, рядом с которой меньше всего койко-мест:");
	echo ("<br>Серийный номер: $serial_number");
	echo ("<br>ID: $place_id");
	echo ("<br>Количество койко-мест: $min_bed");
	echo ("<br>Популярность: $popularity");
	echo ("<br>При условии что рядом - это дистанция не более:  $nearby");
	echo ("<br>При условии что популярная - это популярность более:  $known");
 
 }
 
mssql_close($db);

#================================= Фукнкции ======================================

function get_place_number ($viewer_place_id)

{
	$serial_number = "";
	$sql = "
	select serial_number
	from ref_city_place
	where id like  '%".$viewer_place_id."%'
	";
	$sql = mssql_query($sql);
	while($obResults = mssql_fetch_row($sql)) 
	{
		$serial_number = $obResults[0];
	}
	return $serial_number;
 }	

function get_popularity($place_id)
 {
	$popularity = "";
	$sql = "
	select popularity
	from ref_city_place
	where id like  '%".$place_id."%'
	";
	$sql = mssql_query($sql);
	while($obResults = mssql_fetch_row($sql)) 
	{
		$popularity = $obResults[0];
	}
	return $popularity;
 }	
 
function erase_coma($parametrs_string)
{
	$prdpos = strlen($parametrs_string)-2;
	$parametrs_string = substr_replace($parametrs_string , "", $prdpos, 1 );
	return $parametrs_string;
}

function find_table($table_name)
{
	/*$find = false;
	$sql = "
		select *
		from $table_name
	";
	$sql= mssql_query($sql);
	while($obResults = mssql_fetch_row($sql)) 
	{
		$find = true;
	}*/
	$find = false;
	return $find;
}

function run_zapros($zapros)
{
	$sql = "
				$zapros
			";
	$sql= mssql_query($sql);
}	

?>
</body>
</html>
