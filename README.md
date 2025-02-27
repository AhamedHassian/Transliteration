# **🔠 Phonetic Name Clustering**

This project clusters similar-sounding names using **phonetic algorithms, transliteration, and machine learning** techniques.

## **📌 Installation**  

To run this script, you need to install the required dependencies. You can install them using:  

```bash
pip install -r requirements.txt
```  

If you haven't created `requirements.txt`, install dependencies manually:  

```bash
pip install pandas numpy scikit-learn Levenshtein indic-transliteration metaphone
```  

---

## **📌 Usage**  

### 🔹 **1. Prepare the Dataset**  

- Place your dataset file (**male.csv**) in the same directory as the script.  
- Ensure it contains a column named `names` with the list of names.  

### 🔹 **2. Run the Script**  

Execute the script in your terminal or Jupyter Notebook:  

```bash
python name_clustering.py
```  

### 🔹 **3. Output**  

- The processed dataset will be saved as **processed_male_names_dataset.csv**.  
- The terminal will display the clusters and their representative names.  

---

## **📌 File Structure**  

```bash
📂 Phonetic-Name-Clustering
 ├── male.csv                        # Input dataset (English names)
 ├── processed_male_names_dataset.csv # Output dataset (Clustered names)
 ├── name_clustering.py               # Main Python script
 ├── README.md                        # Project documentation
 ├── requirements.txt                  # List of required dependencies
```  

---

## **📌 Example Output**  

```bash
Clusters and Representative Names:
Cluster 0: ['Arun', 'Arun Kumar', 'Aru'] -> Representative: Aru
Cluster 1: ['Karthik', 'Karthick', 'Karthikeyan'] -> Representative: Karthik
...
```

---

## **📌 Code Explanation**  

### **1️⃣ Name Normalization**  
Removes extra spaces and converts all names to lowercase for consistency.  

```python
df["Normalized_Names"] = df["names"].str.strip().str.lower()
```  

---

### **2️⃣ Transliteration (English → Tamil)**  
If names are in English, they are converted to Tamil using `indic_transliteration`.  

```python
df["Tamil_Names"] = df["Normalized_Names"].apply(
    lambda x: transliterate(x, sanscript.ITRANS, sanscript.TAMIL)
)
```  

---

### **3️⃣ Phonetic Key Generation (Double Metaphone)**  
Each name is converted into a phonetic representation to compare similar-sounding names.  

```python
df["Phonetic_Keys"] = df["Normalized_Names"].apply(lambda x: doublemetaphone(x)[0])
```  

---

### **4️⃣ Compute Pairwise Levenshtein Distances**  
The similarity between phonetic representations is measured using **Levenshtein distance**.  

```python
dist_matrix = np.zeros((num_names, num_names))
for i in range(num_names):
    for j in range(num_names):
        dist_matrix[i, j] = levenshtein_distance(phonetic_keys[i], phonetic_keys[j])
```  

---

### **5️⃣ Agglomerative Clustering**  
Similar names are grouped into clusters based on phonetic similarity.  

```python
clustering = AgglomerativeClustering(
    n_clusters=None, metric="precomputed", linkage="average", distance_threshold=2.0
)
labels = clustering.fit_predict(dist_matrix)
```  

---

### **6️⃣ Assigning a Representative Name for Each Cluster**  
The shortest name in each cluster is chosen as the representative name.  

```python
representative_names = {label: min(cluster, key=len) for label, cluster in clusters.items()}
```  

---

### **7️⃣ Saving the Processed Dataset**  
The final dataset with clusters and representative names is saved as a CSV file.  

```python
df.to_csv("processed_male_names_dataset.csv", index=False, encoding="utf-8")
```  

---

## **📌 Contribution**  

🚀 Feel free to contribute by improving the clustering logic or adding more phonetic algorithms.  
🔹 Open a pull request or submit an issue!  

---

## **📌 License**  

This project is open-source and available under the **MIT License**.
