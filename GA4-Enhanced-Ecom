public function getOrderTrackingCode()
{
    $orderIds = $this->getOrderIds();
    if (!$orderIds) {
        return '';
    }

    $orderCollection = Mage::getResourceModel('sales/order_collection')
        ->addFieldToFilter('entity_id', array('in' => $orderIds));

    $items = array();
    $clientID = Mage::getSingleton('core/session')->getVisitorData('visitor_id');
    if (!$clientID) {
        $clientID = Mage::getModel('core/session')->getSessionId();
    }

    foreach ($orderCollection as $order) {
        $orderId = $order->getIncrementId();

        foreach ($order->getAllVisibleItems() as $item) {
            $items[] = array(
                'id' => $item->getSku(),
                'name' => $item->getName(),
                'price' => $item->getPrice(),
                'quantity' => $item->getQtyOrdered(),
                'category' => '', // Add your category logic here if needed
            );
        }

        // Enhanced eCommerce purchase tracking code
        $transactionData = array(
            'transaction_id' => $orderId,
            'affiliation' => Mage::app()->getStore()->getFrontendName(),
            'value' => $order->getGrandTotal(),
            'tax' => $order->getTaxAmount(),
            'shipping' => $order->getShippingAmount(),
            'currency' => $order->getOrderCurrencyCode(),
        );

        $gaMeasurementId = 'G-LT5G2XTLTF';
        $endpoint = "https://www.google-analytics.com/mp/collect";
        $payloadData = array(
            'client_id' => $clientID,
            'events' => json_encode(array(
                array(
                    'name' => 'purchase',
                    'params' => $transactionData,
                ),
            )),
        );

        // Generate the payload
        $payload = http_build_query($payloadData);

        // Send the payload to Google Analytics
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $endpoint);
        curl_setopt($ch, CURLOPT_POST, true);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $payload);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false); // Adjust this as per your server's SSL configuration
        $response = curl_exec($ch);
        curl_close($ch);

        return "<script>console.log('" . $response . "');</script>";
    }
}
