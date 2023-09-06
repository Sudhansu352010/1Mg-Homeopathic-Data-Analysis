# 1Mg-Homeopathic-Data-Analysis
![image](https://github.com/Sudhansu352010/1Mg-Homeopathic-Data-Analysis/assets/131376814/6dd2a233-22ce-4678-b4dd-23a80c07bfcd)





# Technology & Tools  used
![image](https://github.com/Sudhansu352010/1Mg-Homeopathic-Data-Analysis/assets/131376814/1d4cac22-bcd3-4990-b918-d739138c9396)




# PROBLEM-STATEMENT
Main aim is to analyze the data of 1mg Homeopathic and extract valuable insights that can help us understand the product offerings, pricing, brand specialization, user ratings, and ingredients used in homeopathic medicines. These insights can be used to make informed decisions for opening a new homeopathic medicine store.

# OUR APPROACH FOR THE PROJECT
![image](https://github.com/Sudhansu352010/1Mg-Homeopathic-Data-Analysis/assets/131376814/5f98f0e8-6673-4e78-b3a3-e638740feee5)

# Data Dictionary
![image](https://github.com/Sudhansu352010/1Mg-Homeopathic-Data-Analysis/assets/131376814/6d300e45-3c74-48e4-9ff9-62b925c92163)

# Import All Necessary Libraries of Python
import pandas as pd
import bs4
import requests
from bs4 import BeautifulSoup
headers={
    'User-Agent':'Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36 OPR/95.0.0.0'

}
final_data=[]
for page_number in range(1,21):
    url = f'https://www.1mg.com/categories/homeopathy-57?filter=true&pageNumber=+str(i)'
    response = requests.get(url, headers=headers)
    soup = BeautifulSoup(response.text, 'lxml')
    
    medicine_names = [medicines.text for medicines in soup.find_all('div', class_='style__pro-title___2QwJy')]
    bottle_size = [sizes.text for sizes in soup.find_all('div', class_='style__pack-size___2JQG7')]
    mrp = [m.text for m in soup.find_all('span', class_='style__discount-price___25Bya')]
    prices = [p.text for p in soup.find_all('div', class_='style__price-tag___cOxYc')]
    urls = [ur.find('a')['href'] for ur in soup.find_all('div', class_='style__product-box___liepi')]
    
    for i in range(len(medicine_names)):
        name = medicine_names[i]
        size = bottle_size[i]
        discount_price = mrp[i]
        price = prices[i]
        url = urls[i]
        
        final_data.append([name, size, discount_price, price, url])

df = pd.DataFrame(final_data, columns=['Medicine_name', 'size_of_the_bottle', 'MRP_of_the_bottle', 'Price_of_the_bottle', '1mg_url'])
df
# Check Null Values
df.isnull().sum()

# Check the Data Types
df.dtypes

for i in df.columns:
    print(i,df[i].sort_values().unique(),'\n',sep= '\n')
df['MRP_of_the_bottle']=df['MRP_of_the_bottle'].str.replace('₹','')
df['MRP_of_the_bottle']=df['MRP_of_the_bottle'].astype('int64')
df['Price_of_the_bottle']=df['Price_of_the_bottle'].str.replace('₹','')
df['Price_of_the_bottle']=df['Price_of_the_bottle'].str.replace('MRP','')
df['Price_of_the_bottle']=df['Price_of_the_bottle'].astype('int64')
df.dtypes
pd.DataFrame(df).to_csv("1Mg table1.csv",index = False)

# code for table-2
url_2="https://www.1mg.com"
r=requests.get("https://www.1mg.com/categories/homeopathy-57?filter=true&pageNumber=2",headers=headers)
soup= BeautifulSoup(r.content,"lxml")
soup.prettify()
diff_link_product=[]
for i in range (1,21):
    r=requests.get(f"https://www.1mg.com/categories/homeopathy-57?filter=true&pageNumber={i}",headers=headers)
    soup= BeautifulSoup(r.content,"lxml")
    productlist= soup.find_all("div",{"class":"col-md-3 col-sm-4 col-xs-6 style__container___1TL2R"})
    for product in productlist:
        
        for link in product.find_all("a",href=True):
            
            diff_link_product.append(url_2 + link["href"])
len(diff_link_product)
productlist=[]
for link in diff_link_product:
    r=requests.get(link,headers=headers)
    soup=BeautifulSoup(r.content,"lxml")
    try:
        product_name=soup.find("h1",class_="ProductTitle__product-title___3QMYH").text.strip()
    except:
        product_name:"Nan"
    try:    
        Brand_name=soup.find("div",class_="ProductTitle__manufacturer___sTfon").text.strip()
    except:
        Brand_name:"Nan"
        
    try:
         Rating=soup.find("div",class_="RatingDisplay__ratings-container___3oUuo").text.strip()
    except:
        Rating:"Nan"
    try:
         No_Rating=soup.find("span",class_="RatingDisplay__ratings-header___ZNj5b").text.strip()
    except:
         No_Rating:"Nan"
    try:
        product_details = soup.find_all('div', {'class': 'ProductDescription__description-content___A_qCZ'})

        key_benefits = product_details[0].find_all('li')
        key_benefits_list = [benefit.text.strip() for benefit in key_benefits]
    except:
        key_benefits_list:"Nan"
        
#     ['key_benefits_list'][0][:1]

           
    product={
        
        "Product_name":product_name,
        "Brand":Brand_name,
        "Rating":Rating,
        "No_Rating":No_Rating,
        "key_benefits_list":key_benefits_list
         }
    productlist.append(product)
len(productlist)
df1=pd.DataFrame(productlist)
df1
pd.DataFrame(df1).to_csv("1Mg table2.csv",index = False)
df1['Product_name'].nunique()
