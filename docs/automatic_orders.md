# Automatic orders
One of the most important features of the bot is the possibility to perform orders automatically.

1. To do so, you need to open IB Trader Workstation and login when operating the bot. 

    The API must be enabled [documentation](https://interactivebrokers.github.io/tws-api/initial_setup.html).
2. You need the permission to receive the data from data for the product you want to order. See [IB Market Data](https://www.interactivebrokers.com/en/pricing/research-news-marketdata.php). 

    As you may want to use an automatic strategy for products where you don't have the right, the bot can still inform you via telegram to perform an order "manually". For this, go in trading_bot/settings.py use the constant IB_STOCKEX_NO_PERMISSION to list the stock exchanges where you don't have permission. You can do the same with stocks with IB_STOCK_NO_PERMISSION. It covers especially complex products, where permission is difficult to get.
    
3. If you want to use a simple strategy for a bunch of actions. Let's say that you want to trade Tesla and Amazon with a RSI with threshold 30 and 70. This strategy is called "normal" in the bot.
    
    Select the actions you want to trade. Go in the admin panel and click on Stratcandidates. There should be an item "normal" (otherwise create it). Click on it and select the actions you want to trade (press ctrl to select several).
    
    To perform the orders automatically in trading_bot/settings two parameters must be set. PERFORM_ORDER must be true, in addition you need DIC_PERFORM_ORDER={ "normal":True} 
    
    The strategy used for this is defined in reporting/models.py in the function perform_normal_strat(). 
    
    if self.it_is_index:
       stnormal.stratIndex()
    else:
       stnormal.stratF()
     
    If you want other strategy, you need to create it in the file core/strat.py Look at the function stratRSI() for an example.
     
    To make the combination of simple strategies easier, and make their optimisation easier, an array form is also used. [1, 0 ...] is for instance to use MA as signals for the entry. Look at the function defi_i.
    
    The normal strategy is easy to understand, but it means that you always trade the same stocks.
    
4. If you want to first preselect the stocks you want to trade according to some criteria. Let's say you want to use Formulaic Alphas number 7, called in the code wq7, for stocks listed at Paris. Go in trading_bot/settings.py 

    DIC_PRESEL={
      "Paris":["wq7"],
    }
    
   It will add this preselection to the report. To perform the order automatically set DIC_PERFORM_ORDER={"wq7":True} in addition to PERFORM_ORDER=true.
   The other preselection algorithms can be set the same way. The preselection algorithms can be found in core/bt.py (for backtesting) and in core/btP.py (for production, when differing).
   
   
5. Every time an order is performed, a Telegram message is sent. Generally, the order creation is handled in orders/models.py. The messaging in reporting/telegram.py. As the bot and django are asynchronous, the database is used for the communication between both. Concretelly, the order is performed and a note is left in the Report which order was done. After the report creation is finished, another script read those notes and send the Telegram messages.





