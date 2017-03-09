# PHP-calculateTwoDate
PHP calculate Employee Payroll -working days between (start work and end work) and last month

# calling
$val_workday    = get_workday($driver, $month, $year);//(125,03,2017);

```
//php funtion
<?php
function get_workday($id,$m,$y){
	global $_dbo;
	$info = new stdClass;
	$_dbo->prepare("SELECT dr.DriverID as drid,		
						dr.StartDate AS startdate, 	
						dr.EndDate AS enddate
					FROM drivers as dr
					WHERE dr.DriverID=?");
	$_dbo->param(array($id));
	$info->list = $_dbo->get_object();
	$work_start = '';
	$work_end = '';
	$lastmonth_start = '';
	$lastmonth_end = '';
	foreach($info->list as $va) {
		$v = false;
		$w_work_start = $va->startdate;
		$w_work_end = $va->enddate;
	}
	
	//$lastmonth_start = date('01-m-Y');
	
	$month_ini = new DateTime("first day of last month");
	$month_end = new DateTime("last day of last month");
	
	$m_lastmonth_start = $month_ini->format('Y-m-d');
	$m_lastmonth_end = $month_end->format('Y-m-d');
	//calcuate
	$test=0;
	$holidays=array();
	$total_working_days = 0;
	if($w_work_start != "0000-00-00" && $w_work_end =="0000-00-00"){//have start work date only
		$test=1;
		if($w_work_start <= $m_lastmonth_start){
			//$test=12;
			$total_working_days = calTwoDate($m_lastmonth_start,$m_lastmonth_end);//,$holidays);	
		}else if($w_work_start > $m_lastmonth_start && $w_work_start <= $m_lastmonth_end){
			//$test=13;
			$total_working_days = calTwoDate($w_work_start,$m_lastmonth_end);//'2017-02-25','2017-02-28');//($w_work_start,$m_lastmonth_end,$holidays);
		}
	}else if($w_work_start != '0000-00-00' && $w_work_end !='0000-00-00'){//have both start and end work date only
		if($w_work_start <= $w_work_end){
			$test=2;
			if($w_work_start <= $m_lastmonth_start && $w_work_end <= $m_lastmonth_start){
				$total_working_days = calTwoDate($m_lastmonth_start,$m_lastmonth_end);//,$holidays);
			}elseif($w_work_start < $m_lastmonth_start && ($w_work_end >= $m_lastmonth_start && $w_work_end <= $m_lastmonth_end ) ){
				$total_working_days = calTwoDate($m_lastmonth_start,$w_work_end);//,$holidays);
			}elseif($w_work_start >= $m_lastmonth_start && $w_work_end <= $m_lastmonth_end){
				$total_working_days = calTwoDate($w_work_start,$w_work_end);//,$holidays);
			}elseif( ($w_work_start >= $m_lastmonth_start && $w_work_start <= $m_lastmonth_end) && $w_work_end > $m_lastmonth_end){
				$total_working_days = calTwoDate($w_work_start,$m_lastmonth_end);//,$holidays);
			}
		}
	}
	//ennd calculate
	return $total_working_days;//total_working_days;//$_dbo->get_assoc(MySQL::ROW);
}

function calTwoDate($start, $end){	
	$total = 0;
	$dif   = 0;
	$start_date = strtotime($start);
	$end_date = strtotime($end);
	$datediff = $end_date - $start_date;	
	$dif = floor($datediff / (60 * 60 * 24));	
	if($dif >= 0) $total = $dif + 1;
	return $total;
}
?>
```
