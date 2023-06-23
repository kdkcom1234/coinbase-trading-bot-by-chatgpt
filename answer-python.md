먼저, 코인베이스 API를 사용하려면 라이브러리를 설치해야합니다. `cbpro`를 사용하겠습니다. 이 라이브러리를 설치하려면 터미널에 다음 명령을 입력하세요:

```bash
pip install cbpro
```

다음으로, API 키, 시크릿, 패스프레이즈를 생성해야 합니다. 이것들은 Coinbase Pro 계정의 API 섹션에서 생성할 수 있습니다. API 키는 권한을 주어서 굉장히 중요합니다. 절대로 이것들을 다른 사람과 공유하지 마세요.

다음은 간단한 봇 예제입니다. 이 봇은 BTC의 가격이 35000 이하가 되면 BTC를 구매하고, 가격이 45000 이상이 되면 판매합니다.

```python
import cbpro
import time

public_client = cbpro.PublicClient()

class TradingBot(cbpro.AuthenticatedClient):

    def __init__(self, *args, **kwargs):
        super(TradingBot, self).__init__(*args, **kwargs)

        self.buy_threshold = 35000.00
        self.sell_threshold = 45000.00
        self.base_currency = 'BTC'
        self.quote_currency = 'USD'
        self.product_id = f'{self.base_currency}-{self.quote_currency}'

    def get_current_price(self):
        response = public_client.get_product_ticker(product_id=self.product_id)
        return float(response['price'])

    def should_buy(self):
        return self.get_current_price() <= self.buy_threshold

    def should_sell(self):
        return self.get_current_price() >= self.sell_threshold

    def place_order(self, side):
        order = {
            'product_id': self.product_id,
            'side': side,
            'type': 'market',
            'funds': '100.00'
        }
        self.create_order(order)

    def start_trading(self):
        while True:
            print("Checking prices...")
            if self.should_buy():
                print("Price is low, buying...")
                self.place_order('buy')

            if self.should_sell():
                print("Price is high, selling...")
                self.place_order('sell')

            time.sleep(60)  # sleep for a minute before checking again

API_KEY = '<Your-API-Key>'
API_SECRET = '<Your-API-Secret>'
API_PASS = '<Your-API-Passphrase>'

bot = TradingBot(API_KEY, API_SECRET, API_PASS)
bot.start_trading()
```

이 코드는 매우 단순화되었으며 실제 상황에서는 굉장히 복잡할 수 있습니다. 이것은 단순히 구조를 보여주는 예제입니다. 코인베이스 API에 대한 보다 완벽한 이해가 필요하고, 주식 거래는 많은 요소를 고려해야하므로 이 코드를 실제로 사용하기 전에 충분히 이해하고 검토하십시오. 

또한 코인베이스 API는 요청 제한을 가지고 있으므로

 이를 고려하십시오. 너무 많은 요청을 보내면 계정이 일시적으로 사용 제한 될 수 있습니다. 이러한 제한을 피하려면 적절한 시간 간격으로 요청을 보내야 합니다. 위의 코드에서는 60초마다 한 번씩 체크하도록 설정되어 있습니다. 

마지막으로, 이 코드는 어떤 실제 금전적 이익을 보장하거나 제공하지 않습니다. 이는 학습 목적으로만 사용해야 합니다. 실제 투자를 하기 전에 충분한 연구와 준비가 필요합니다. 
