# eco-rentals

Creating a Python web application for eco-rentals that optimizes short-term rental pricing based on sustainable factors can be broken down into several steps. We'll use Flask to create the web application and assume a simple logic for optimizing the pricing. For demonstration purposes, this will include:

1. A basic Flask setup.
2. A form for inputting rental data.
3. A simple algorithm for calculating optimal pricing.
4. Sustainability factors to be considered.
5. Proper comments and error handling.

Below is a fully commented Python program implementing the above requirements:

```python
from flask import Flask, request, jsonify, render_template
import logging

# Setup logging
logging.basicConfig(level=logging.INFO)

app = Flask(__name__)

# Mock function to calculate optimal pricing based on sustainability factors
def calculate_optimal_price(base_price, sustainability_factors):
    try:
        # Simple pricing logic considering sustainability factors
        # For demonstration purposes: reduce price by 10% for each positive sustainability factor
        discount = 0.1 * sum(sustainability_factors.values())
        optimal_price = base_price * (1 - discount)
        
        # Ensure price does not go below a minimum threshold
        if optimal_price < base_price * 0.5:
            optimal_price = base_price * 0.5
        
        return round(optimal_price, 2)
    except Exception as e:
        logging.error(f"Error in calculating optimal price: {e}")
        # If there's an error, return base price as fallback
        return base_price

@app.route('/', methods=['GET', 'POST'])
def eco_rentals():
    if request.method == 'POST':
        try:
            # Get the user input
            base_price = float(request.form.get('base_price', 0))
            sustainability_factors = {
                'solar_energy': bool(request.form.get('solar_energy')),
                'recycling_program': bool(request.form.get('recycling_program')),
                'water_conservation': bool(request.form.get('water_conservation')),
            }
            
            # Calculate the optimal price
            optimal_price = calculate_optimal_price(base_price, sustainability_factors)

            # Return the result
            return render_template('result.html', optimal_price=optimal_price)

        except Exception as e:
            logging.error(f"Error processing request: {e}")
            return "An error occurred. Please try again.", 500

    # Render the form for input
    return render_template('form.html')

@app.route('/api/price', methods=['POST'])
def api_price():
    try:
        data = request.json
        base_price = data.get('base_price', 0)
        sustainability_factors = data.get('sustainability_factors', {})

        # Calculate optimal price
        optimal_price = calculate_optimal_price(base_price, sustainability_factors)
        
        # Return JSON response
        return jsonify({"optimal_price": optimal_price}), 200

    except Exception as e:
        logging.error(f"Error processing API request: {e}")
        return jsonify({"error": "An error occurred while processing your request."}), 500

if __name__ == '__main__':
    app.run(debug=True)
```

### Additional Files

**Templates (`form.html`, `result.html`)**

Create a `templates` directory within the same directory as your Python script, and within it, add two files: `form.html` and `result.html`.

**form.html**
```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Eco Rentals - Pricing</title>
</head>
<body>
    <h1>Eco Rentals Pricing Calculator</h1>
    <form method="post">
        <label>Base Price: <input type="number" name="base_price" required step="0.01"></label><br>
        <label>Solar Energy: <input type="checkbox" name="solar_energy"></label><br>
        <label>Recycling Program: <input type="checkbox" name="recycling_program"></label><br>
        <label>Water Conservation: <input type="checkbox" name="water_conservation"></label><br>
        <input type="submit" value="Calculate Optimal Price">
    </form>
</body>
</html>
```

**result.html**
```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Eco Rentals - Result</title>
</head>
<body>
    <h1>Optimal Price Calculation</h1>
    <p>The optimal rental price is: ${{ optimal_price }}</p>
    <a href="/">Calculate again</a>
</body>
</html>
```

### Notes

- **Flask Setup**: Make sure you have Flask installed via `pip install flask`.
- **Error Handling**: Both server errors and form handling errors are logged and managed.
- **Sustainability Factors**: This example considers three factors, but you can extend this set.
- **Algorithm**: The discount logic here is simple. For a real-world application, more complex models based on data analysis would be typical.

This setup provides a foundational web application for calculating rental prices based on sustainability factors, with room for expansion and refinement.