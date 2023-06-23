자바로 코인베이스 API를 이용한 간단한 트레이딩 봇을 만들기 위해, Coinbase Pro 자바 클라이언트 라이브러리인 `coinbase-pro-java`를 사용하겠습니다. 

Maven 프로젝트에서는 pom.xml 파일에 의존성을 추가하면 됩니다.

```xml
<dependencies>
    <dependency>
        <groupId>info.bitrich.xchangestream</groupId>
        <artifactId>xchange-coinbasepro</artifactId>
        <version>4.4.2</version>
    </dependency>
</dependencies>
```

그런 다음, 다음은 Coinbase API를 사용하여 BTC 가격이 35000 이하일 때 구매하고 45000 이상일 때 판매하는 간단한 봇의 코드입니다:

```java
import org.knowm.xchange.Exchange;
import org.knowm.xchange.ExchangeFactory;
import org.knowm.xchange.coinbasepro.CoinbaseProExchange;
import org.knowm.xchange.coinbasepro.service.CoinbaseProMarketDataService;
import org.knowm.xchange.currency.CurrencyPair;
import org.knowm.xchange.dto.marketdata.Ticker;
import org.knowm.xchange.service.marketdata.MarketDataService;

public class TradingBot {

    private static final double BUY_THRESHOLD = 35000.00;
    private static final double SELL_THRESHOLD = 45000.00;
    private static final CurrencyPair CURRENCY_PAIR = new CurrencyPair("BTC/USD");

    public static void main(String[] args) throws Exception {
        Exchange exchange = ExchangeFactory.INSTANCE.createExchange(CoinbaseProExchange.class.getName());

        // Provide your API key and secret
        exchange.getExchangeSpecification().setApiKey("<Your-API-Key>");
        exchange.getExchangeSpecification().setSecretKey("<Your-API-Secret>");

        MarketDataService marketDataService = new CoinbaseProMarketDataService(exchange);

        while (true) {
            Ticker ticker = marketDataService.getTicker(CURRENCY_PAIR);
            System.out.println("Current price: " + ticker.getLast());

            if (ticker.getLast().doubleValue() <= BUY_THRESHOLD) {
                System.out.println("Price is low, buying...");
                // Add your logic to buy
            }

            if (ticker.getLast().doubleValue() >= SELL_THRESHOLD) {
                System.out.println("Price is high, selling...");
                // Add your logic to sell
            }

            Thread.sleep(60000); // sleep for a minute before checking again
        }
    }
}
```

이 코드는 매우 기본적이며 구매 및 판매 로직이 구현되지 않았습니다. 실제 트레이딩 봇을 만들기 위해서는 이러한 로직을 구현해야하며, 또한 봇이 실행되는 동안 발생할 수 있는 여러 가지 예외 상황을 처리해야합니다. 또한, 금융 거래에 대한 충분한 지식이 필요합니다.

마지막으로, 코인베이스 API는 요청 제한을 가지고 있으므로 이를 고려하십시오. 너무 많은 요청을 보내면 계정이 일시적으로 사용 제한 될 수 있습니다

. 이러한 제한을 피하려면 적절한 시간 간격으로 요청을 보내야 합니다. 위의 코드에서는 60초마다 한 번씩 체크하도록 설정되어 있습니다.
