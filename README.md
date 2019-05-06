# GoldenMoa

This Hadoop MapReduce calculates the most profitable items to flip in GW2.

## How to Run

_Prerequsite(s):_ Installed Hadoop MapReduce, started all services.

1.  Create a jar file by running the following in the main folder where `pom.xml` is located.
    ```
    mvn clean install
    ```
    This creates a jar with the relative path `target/golden-moa-1.0.jar`

2.  Generate our dataset from current trading post data. Optional arguments: `--threshold`.
    ```
    // Usage: hadoop jar JAR gw2.GW2SpidyMain [--threshold THRESHOLD]
    // [--threshold THRESHOLD]  default: 100 
    hadoop jar target/golden-moa-1.0.jar gw2.GW2SpidyMain
    ```
    This generates `allitemsdataset.txt`.
    
3.  Create input folder and add dataset.
    ```
    hadoop fs -mkdir -p ~/input/
    hadoop fs -put allitemsdataset.txt ~/input/
    
    // We can see the file has been successfully added.
    hadoop fs -ls ~/input/
    ```

4.  Run MapReduce. Optional arguments: `--minbuycount`.
    ```
    // Usage: hadoop jar JAR GW2Job INPUT OUTPUT [--minbuycount MINBUY] 
    // [--minbuycount MINBUY]   default: 500
    hadoop jar target/golden-moa-1.0.jar GW2Job ~/input/ ~/output/
    ```
    
5.  View output of the MapReduce.
    ```
    // Copy output files locally to view
    hadoop fs -copyToLocal ~/output/ .
    
    // View top 10, lowest profit items
    cat output/part-r-00000 | sort -n -k1 | head -n10
    
    // Clean up
    hadoop fs -rm -r ~/output/
    ```

## Example MapReduce Result

Result from `cat output/part-r-00000 | sort -n -k1 | head -n10`. These are lowest profitable items with buy counts over 
500 and mapped into 1 silver buckets which are the default values.
```
246    Name: Rampager's Rogue Pants of Divinity Sell Price: 2000 Buy Price: 1300 Sell Count: 746 Buy Count: 859
449    Name: Ruby Platinum Earring Sell Price: 2200 Buy Price: 1200 Sell Count: 467 Buy Count: 571
450    Name: Rampager's Iron Shield of the Geomancer Sell Price: 2600 Buy Price: 1400 Sell Count: 31 Buy Count: 740
552    Name: Mighty Bronze Greatsword Sell Price: 3800 Buy Price: 1300 Sell Count: 303 Buy Count: 553
576    Name: Valkyrie Masquerade Leggings Sell Price: 6000 Buy Price: 1500 Sell Count: 272 Buy Count: 699
601    Name: Berserker's Seer Mantle of Vampirism Sell Price: 3600 Buy Price: 2300 Sell Count: 439 Buy Count: 1354
656    Name: Rampager's Gladiator Helm Sell Price: 1900 Buy Price: 1600 Sell Count: 339 Buy Count: 704
713    Name: Carnelian Silver Amulet Sell Price: 7200 Buy Price: 1700 Sell Count: 430 Buy Count: 732
766    Name: Penetrating Krytan Greatsword of the Night Sell Price: 16300 Buy Price: 1600 Sell Count: 83 Buy Count: 594
985    Name: Rejuvenating Steel Plated Inscription Sell Price: 5100 Buy Price: 2700 Sell Count: 785 Buy Count: 1310
```
