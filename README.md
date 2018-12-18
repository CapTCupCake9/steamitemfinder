#Steam api key 64E3B0F4C689CFC6634EDA857FB7D2AD
import requests, urllib, math, time

'''
TODO:
  * add all currencies supported by the Steam Marketplace to `curAbbrev`
  * create docstrings for all functions
  * listings parser; get total number of listings (`total_count` in JSON)
  * get price overview via http://steamcommunity.com/market/priceoverview/
'''

class MarketListing:
    def __init__(self, listing_id, price):
        self._id = listing_id
        self.price = price

class MarketItem:
    def __init__(self, item_id=None, listings=None):
        self._id = item_id
        if listings:
            self.listings = listings
        else:
            self.listings = []

    def add_listing(self, l_id, price):
        self.listings.append(MarketListing(l_id, price))

# Currency abbreviations
curAbbrev = {
    'USD' : 1,
    'GBP' : 2,
    'EUR' : 3,
    'CHF' : 4,
    'RUB' : 5,
    'KRW' : 16,
    'CAD' : 20,
}

"""
Gets item listings from the Steam Marketplace.

@param game_id: ID of game item belongs to.
@param item: Name of item to lookup.
@param start: Listing index to start from. 0 is the most recent listing.
@param start: number >= 0
@param count: Number of listings to grab, start and beyond.
@param count: number >= 1
@param currency: Abbreviation of currency to return listing prices in.
@type currency:
    Accepted currencies:

      - USD
      - GBP
      - EUR
      - CHF
      - RUB
      - KRW
      - CAD

    Please lookup the proper abbreviation for your currency of choice.
@return A list of prices. Depending on your chosen currency, you may need to
    move the decimal place. For instance, in USD, $25.98 would be returned
    as 2598
"""
def get_item(game_id, item, start=0, count=10, currency='USD'):
    url = 'http://steamcommunity.com/market/listings/{}/{}/render'.format(
        game_id,
        urllib.parse.quote(item)
    )
    payload = {
        'start' : start,
        'count' : count,
        'currency' : curAbbrev[currency]
    }
    resp = requests.get(url, params=payload)
    listings = resp.json()['listinginfo']
    market_item = MarketItem()
    for l_id, v in listings.items():
        price = v['converted_price_per_unit'] + v['converted_fee_per_unit']
        market_item.add_listing(l_id, price)
    return market_item

def get_tf2_item(item, start=0, count=10, currency='USD'):
    return get_item('440', item, start, count)

def get_csgo_item(item, start=0, count=10, currency='USD'):
    return get_item('730', item, start, count)

def get_rust_item(item, start=0, count=1, currency='USD'):
    return get_item('252490', item, start, count)

#Main
items7 = ['Portrait Picture Frame', 'Ammo Wooden Box', 'Black Hoodie', 'Snow Camo Pants', 'Two Sided Town Sign', 'Tan Boots', 'Metal', 'Metal', 'Agony Yellow', 'Black Boots', 'Ancient Furnace', 'Single Sign Post', 'Ammo Wooden Box', 'Wood Camo Sleeping Bag', 'Tan Boots', 'Black Bandana', 'Toxic', 'Cobalt Graffiti Door', 'Forest Camo Pants', "Junker's Vest", 'One Sided Town Sign Post']
items = ['XL Picture Frame', 'One Sided Town Sign Post', 'Cloth', 'Cloth', 'Cloth', 'Cloth', 'Cloth', 'Cloth', 'Cloth', 'Cloth', 'Cloth', 'Cloth', 'Wood', 'Wood', 'Wood', 'Wood', 'Wood', 'Wood', 'Wood', 'Wood', 'Wood', 'Wood', 'Wood', 'Green Jacket', 'Blue Tshirt', 'Hunting Jacket', 'Blue Beenie Hat', 'Orange Longsleeve T-Shirt', 'Red Jacket', 'Forest Camo Pants', 'Red Jacket', 'Urban Camo Pants', 'Tan Boots', 'Black Hoodie', 'Blue Jeans', 'Red Beenie Hat', 'Grey Cap', 'Landscape Picture Frame', 'Tall Picture Frame', 'Double Sign Post', 'Desert Jacket', 'Blue Tshirt', 'Red Jacket', 'Multicam Jacket', 'Metal', 'Blue Cap', 'Snowcamo Jacket', 'Orange Longsleeve T-Shirt', 'Royal Wooden Box', 'Green Armored Container Door', 'Ammo Wooden Box', 'Yellow Longsleeve T-Shirt', 'Red Jacket', 'First Aid Green', 'Blue Tshirt', 'Ammo Wooden Box', 'Black Beenie Hat', 'Desert Camo Bandana', 'Toxic', 'Midnight Dream', 'Large Banner on pole', 'Overlord Mask', 'Green Hoodie', 'Chieftain Pump Shotgun', 'Ser Winter TShirt', 'Bulletstorm', 'Tan Boots', 'Orange Longsleeve T-Shirt', 'Green Cap', 'Blue Cap', 'Two Sided Town Sign Post']
listvalue = []
count = 0
total_value = 0
items2 = ['Forest Camo Pants','Tan Boots','Large Banner on pole','Green Hoodie','Green Beenie Hat','Two Sided Town Sign Post','XXL Picture Frame','Hunting Jacket','Black Beenie Hat','Single Sign Post','Green Jacket','Phoenix Crossbow','Golden Drake AK47','Wood','Blue Beenie Hat','Blue Jacket','Two Sided Ornate Hanging Sign','Snowcamo Jacket','Red Beenie Hat']
print(len(items))
for prize in items:
    item = get_rust_item(items[count])
    time.sleep(0.3)
    count += 1
    if count == 71:
        break

    print("Progress: " + items[count])
    for listing in item.listings:
        value = listing.price
        listvalue.append(value)

for cent in listvalue:
    final_total = total_value = cent + total_value

print("Total Value Of Your Rust Inventory: " + "$" + str(float(final_total / 100)))
