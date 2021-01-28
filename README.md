# อัพเดท Wallet อั่งเปา วันที่ 29 / มกราคม /2021

```php
  function vocherwalletcheck($url, $tel) {
        $curl = curl_init();
        curl_setopt_array($curl, array(
        CURLOPT_URL => "https://gift.truemoney.com/campaign/vouchers/".$url."/verify?mobile=".$tel,
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_ENCODING => "",
        CURLOPT_MAXREDIRS => 10,
        CURLOPT_TIMEOUT => 0,
        CURLOPT_FOLLOWLOCATION => true,
        CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
        CURLOPT_CUSTOMREQUEST => "GET",
        ));
        $response = curl_exec($curl);
        curl_close($curl);
        $response = json_decode($response, true);
        if ($response['status']['code'] == "VOUCHER_OUT_OF_STOCK") {
            $message['status'] = "error";
            $message['message'] = "ลิงค์ซองของขวัญถูกใช้งานแล้ว";
        } else if ($response['status']['code'] == "INVALID_LINK" || $response['status']['code'] == "VOUCHER_NOT_FOUND"){
            $message['status'] = "error";
            $message['message'] = "ลิงค์ซองของขวัญไม่ถูกต้อง";
        } else if ($response['status']['code'] == "CANNOT_GET_OWN_VOUCHER"){
            $message['status'] = "error";
            $message['message'] = "ไม่สามารถใช้ code ตัวเองได้";
        } else if ($response['status']['code']=="SUCCESS") {
            if($response['data']['voucher']['member'] != 1){
                $message['status'] = "error";
                $message['message'] = "กรุณาเลือกให้รับซองได้เพียวคนเดียว";
            } else {
                $message['status'] = "success";
                $message["message"] = "สำเร็จผ่านแล้วอิอิซ่า";
                $message["amount_baht"] = str_replace(',','', $response['data']['voucher']['amount_baht']);
            }
        } else {
            $message['status'] = "error";
            $message['message'] = "เกิดข้อผิดพลาดกรุณากรอกข้อมูลให้ถูกต้อง";
        }
        return $message;
    }

    function vocherwalletsend($url, $tel) {
        $curl = curl_init();
        $data = array("mobile"=> $tel ,"voucher_hash"=> $url);
        curl_setopt_array($curl, array(
        CURLOPT_URL => "https://gift.truemoney.com/campaign/vouchers/".$url."/redeem",
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_POSTFIELDS => json_encode($data),
        CURLOPT_HTTPHEADER => array(
            'Content-Type:application/json',
        ),
        CURLOPT_ENCODING => "",
        CURLOPT_MAXREDIRS => 10,
        CURLOPT_TIMEOUT => 0,
        CURLOPT_FOLLOWLOCATION => true,
        CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
        CURLOPT_CUSTOMREQUEST => "POST",
        ));
        $redeemd = curl_exec($curl);
        $redeem = json_decode($redeemd, true);
        curl_close($curl);
        if ($redeem['status']['code'] == "SUCCESS" && $redeem['data']['redeemer_profile']['mobile_number'] == $tel) {
            $response['status'] = "success";
        } else {
            $response['status'] = "error";
            $response['message'] = "ลิงก์อ่างเปาไม่ถูกต้อง";
        }
        return $response;
    }
```
