<pre>
import time
import random
import pandas as pd

class RealtimePricingAlgorithm:
    def __init__(self, initial_price, demand_sensitivity, competition_factor, time_decay_factor):
        """
        Initializes the pricing algorithm with key parameters.

        Args:
            initial_price: The starting price for the product.
            demand_sensitivity: How much price affects demand (higher value = more sensitive).
            competition_factor: Weight given to competitor prices in pricing decisions.
            time_decay_factor: How quickly past demand data loses influence.
        """
        self.price = initial_price
        self.demand_sensitivity = demand_sensitivity
        self.competition_factor = competition_factor
        self.time_decay_factor = time_decay_factor
        self.demand_history = []  # Store historical demand data

    def update_price(self, current_demand, competitor_prices):
        """
        Updates the product price based on current demand, competitor prices, and historical data.

        Args:
            current_demand: The current demand for the product.
            competitor_prices: A list of prices from competing products.
        """
        # 1. Calculate weighted average of competitor prices
        avg_competitor_price = sum(competitor_prices) / len(competitor_prices) if competitor_prices else self.price

        # 2. Update demand history with time decay
        self.demand_history.append(current_demand)
        if len(self.demand_history) > 10:  # Adjust history length as needed
            self.demand_history = self.demand_history[-10:]
        weighted_demand = sum([d * (self.time_decay_factor ** i) for i, d in enumerate(self.demand_history[::-1])]) / sum([self.time_decay_factor ** i for i in range(len(self.demand_history))])

        # 3. Calculate price adjustment based on demand and competition
        price_adjustment = (weighted_demand - current_demand) * self.demand_sensitivity 
        price_adjustment += (avg_competitor_price - self.price) * self.competition_factor

        # 4. Adjust price with constraints (e.g., minimum/maximum price)
        new_price = self.price + price_adjustment
        new_price = max(new_price, 5)  # Example: Minimum price of 5
        new_price = min(new_price, 100)  # Example: Maximum price of 100

        # 5. Update current price
        self.price = new_price

    def get_current_price(self):
        """
        Returns the current price of the product.
        """
        return self.price

# Example usage
if __name__ == "__main__":
    # Initialize the pricing algorithm
    pricing_algorithm = RealtimePricingAlgorithm(initial_price=50, 
                                                demand_sensitivity=0.05, 
                                                competition_factor=0.2, 
                                                time_decay_factor=0.8)

    # Simulate real-time pricing updates
    for _ in range(100):
        current_demand = random.randint(10, 100)  # Simulate random demand
        competitor_prices = [random.randint(40, 60) for _ in range(3)]  # Simulate competitor prices
        pricing_algorithm.update_price(current_demand, competitor_prices)
        print(f"Time: {time.time()}, Price: {pricing_algorithm.get_current_price()}")
</pre>
