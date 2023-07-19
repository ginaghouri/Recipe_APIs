import pprint
import requests
import json
import sys

sys.stdin.reconfigure(encoding='utf-8')
sys.stdout.reconfigure(encoding='utf-8')

# Take the input from the user
ingredient = input('What ingredient should be in recipes? ')

# Helper function to create and write JSON file
def create_and_write_json_file(data, filename):
    with open(filename, 'w', encoding='utf-8') as file:
        json.dump(data, file)
    print(f"Data written to '{filename}' file.")

def recipe_search(ingredient):
    app_id = 'a2a43e33'
    app_key = '7230c73e5b23cf4676d66b140694077c'
    url = 'https://api.edamam.com/search?q={}&app_id={}&app_key={}'.format(ingredient, app_id, app_key)
    result = requests.get(url)
    data = result.json()
    return data['hits']

def save_recipes(recipes, fileName, log=False):
    with open(fileName, 'w') as file:
        output = ''
        for recipe_data in recipes:
            recipe = recipe_data['recipe']

            output += recipe['label'] + '\n'
            output += recipe['shareAs'] + '\n'
            output += 'Total Calories (KCAL): ' + str(recipe['totalNutrients']['ENERC_KCAL']['quantity']) + '\n'
            output += 'Total Weight: ' + str(recipe['totalWeight']) + 'g' + '\n\n'

        if log:
            print(output)

        file.write(output)
    print(f'>> Recipe data stored in {fileName}.')

def choose_a_dish(recipes):
    kcals = input('Approximately how much kcal must be in dish (type max amount): ')
    for recipe_data in recipes:
        recipe = recipe_data['recipe']
        if recipe['totalNutrients']['ENERC_KCAL']['quantity'] <= float(kcals):
            print('That dish below is perfect choice ')
            print(recipe['label'])
            print(recipe['shareAs'])
            print('Total Weight:', recipe['totalWeight'], 'g')
            print('Total Kilo-Calories', recipe['totalNutrients']['ENERC_KCAL']['quantity'])
            break
    else:
        print('Sorry, no dish found according to your parameters, please change your search parameters. ')

def nutr_search(ingredient):
    app_id = '4dd82c8f'
    app_key = '81f89aaea599c315e3efb572bbbabf61'
    url = 'https://api.edamam.com/api/nutrition-details?={}&app_id={}&app_key={}'.format(ingredient, app_id, app_key)
    result = requests.post(url)
    data = result.json()
    create_and_write_json_file(data, 'nutritionApiData.json')

def search_ingredient_tasty(ingredient):
    tasty_api_key = '21a0a5dd00msh09f929265c8bdd7p115571jsn57d230a7f819'
    endpoint = "https://tasty.p.rapidapi.com/recipes/list"
    headers = {
        "X-RapidAPI-Key": tasty_api_key
    }
    params = {
        #'prefix': 'apple chicken spice'
        'q': ingredient
    }

    response = requests.get(endpoint, headers=headers, params=params)

    if response.status_code == 200:
        data = response.json()
        return data
    else:
        print("Error: Unable to fetch search results from Tasty API.")
        return None

def run():
    results = recipe_search(ingredient)

    # Extension 1 - Save results to a file
    save_recipes(results, 'recipes.txt')

    # Extension 2 - Sort and save results based on 'totalWeight'
    sorted_results = sorted(results, key=lambda x: x['recipe']['totalWeight'], reverse=True)
    save_recipes(sorted_results, 'sorted_recipes.txt')

    # Extension 3 - Ask the user additional question(s)
    choose_a_dish(results)

    # Extension 4 - Use the nutrition API
    nutr_search(ingredient)

    # Extension 5 - Use a different API
    search_results = search_ingredient_tasty(ingredient)

    if search_results:
        print("\nSearch results from Tasty API:")
        for result in search_results['results']:
            # Some results don't have 'nutrition' key so to avoid errors
            if 'calories' in result['nutrition']:
                print(result['name'])
                print('Total Calories:', result['nutrition']['calories'])
                print()
    else:
        print("No search results found from Tasty API.")

run()
