---
layout: post
title: "2"
author: "장현정"
description: "목록 항목 설명"
---
{
 "cells": [
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Coordinate Reference Systems\n",
    "It's pretty amazing that we can represent the Earth's surface in 2 dimensions!"
   ]
  },
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Introduction\n",
    "The maps you create in this course portray the surface of the earth in two dimensions. But, as you know, the world is actually a three-dimensional globe. So we have to use a method called a `map projection` to render it as a flat surface.\n",
    "\n",
    "Map projections can't be 100% accurate. Each projection distorts the surface of the Earth in some way, while retaining some useful property. For instance,\n",
    "\n",
    "- the equal-area projections (like \"Lambert Cylindrical Equal Area\", or \"Africa Albers Equal Area Conic\") preserve area. This is a good choice, if you'd like to calculate the area of a country or city, for example.\n",
    "- the equidistant projections (like \"Azimuthal Equidistant projection\") preserve distance. This would be a good choice for calculating flight distance."
   ]
  },
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We use a `coordinate reference system (CRS)` to show how the projected points correspond to real locations on Earth. In this tutorial, you'll learn more about coordinate reference systems, along with how to use them in GeoPandas."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {},
   "outputs": [],
   "source": [
    "import geopandas as gpd\n",
    "import pandas as pd"
   ]
  },
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Setting the CRS\n",
    "When we create a GeoDataFrame from a shapefile, the CRS is already imported for us."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "epsg:32630\n"
     ]
    }
   ],
   "source": [
    "# Load a GeoDataFrame containing regions in Ghana\n",
    "regions = gpd.read_file(\"data/ghana/ghana/Regions/Map_of_Regions_in_Ghana.shp\")\n",
    "print(regions.crs)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Region</th>\n",
       "      <th>geometry</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>POLYGON ((686446.075 842986.894, 686666.193 84...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>Brong Ahafo</td>\n",
       "      <td>POLYGON ((549970.457 968447.094, 550073.003 96...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>Central</td>\n",
       "      <td>POLYGON ((603176.584 695877.238, 603248.424 69...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>Eastern</td>\n",
       "      <td>POLYGON ((807307.254 797910.553, 807311.908 79...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>Greater Accra</td>\n",
       "      <td>POLYGON ((858081.638 676424.913, 858113.115 67...</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "          Region                                           geometry\n",
       "0        Ashanti  POLYGON ((686446.075 842986.894, 686666.193 84...\n",
       "1    Brong Ahafo  POLYGON ((549970.457 968447.094, 550073.003 96...\n",
       "2        Central  POLYGON ((603176.584 695877.238, 603248.424 69...\n",
       "3        Eastern  POLYGON ((807307.254 797910.553, 807311.908 79...\n",
       "4  Greater Accra  POLYGON ((858081.638 676424.913, 858113.115 67..."
      ]
     },
     "execution_count": 3,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "regions.head()"
   ]
  },
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "How do you interpret that?\n",
    "\n",
    "Coordinate reference systems are referenced by European Petroleum Survey Group (EPSG) codes.\n",
    "\n",
    "This GeoDataFrame uses EPSG 32630, which is more commonly called the \"Mercator\" projection. This projection preserves angles (making it useful for sea navigation) and slightly distorts area.\n",
    "\n",
    "However, when creating a GeoDataFrame from a CSV file, we have to set the CRS. EPSG 4326 corresponds to coordinates in latitude and longitude."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "c:\\Users\\hyeonjung\\anaconda3\\lib\\site-packages\\pyproj\\crs\\crs.py:141: FutureWarning: '+init=<authority>:<code>' syntax is deprecated. '<authority>:<code>' is the preferred initialization method. When making the change, be mindful of axis order changes: https://pyproj4.github.io/pyproj/stable/gotchas.html#axis-order-changes-in-proj-6\n",
      "  in_crs_string = _prepare_from_proj_string(in_crs_string)\n"
     ]
    },
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Region</th>\n",
       "      <th>District</th>\n",
       "      <th>FacilityName</th>\n",
       "      <th>Type</th>\n",
       "      <th>Town</th>\n",
       "      <th>Ownership</th>\n",
       "      <th>Latitude</th>\n",
       "      <th>Longitude</th>\n",
       "      <th>geometry</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Offinso North</td>\n",
       "      <td>A.M.E Zion Clinic</td>\n",
       "      <td>Clinic</td>\n",
       "      <td>Afrancho</td>\n",
       "      <td>CHAG</td>\n",
       "      <td>7.40801</td>\n",
       "      <td>-1.96317</td>\n",
       "      <td>POINT (-1.96317 7.40801)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Bekwai Municipal</td>\n",
       "      <td>Abenkyiman Clinic</td>\n",
       "      <td>Clinic</td>\n",
       "      <td>Anwiankwanta</td>\n",
       "      <td>Private</td>\n",
       "      <td>6.46312</td>\n",
       "      <td>-1.58592</td>\n",
       "      <td>POINT (-1.58592 6.46312)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Adansi North</td>\n",
       "      <td>Aboabo Health Centre</td>\n",
       "      <td>Health Centre</td>\n",
       "      <td>Aboabo No 2</td>\n",
       "      <td>Government</td>\n",
       "      <td>6.22393</td>\n",
       "      <td>-1.34982</td>\n",
       "      <td>POINT (-1.34982 6.22393)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Afigya-Kwabre</td>\n",
       "      <td>Aboabogya Health Centre</td>\n",
       "      <td>Health Centre</td>\n",
       "      <td>Aboabogya</td>\n",
       "      <td>Government</td>\n",
       "      <td>6.84177</td>\n",
       "      <td>-1.61098</td>\n",
       "      <td>POINT (-1.61098 6.84177)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Kwabre</td>\n",
       "      <td>Aboaso Health Centre</td>\n",
       "      <td>Health Centre</td>\n",
       "      <td>Aboaso</td>\n",
       "      <td>Government</td>\n",
       "      <td>6.84177</td>\n",
       "      <td>-1.61098</td>\n",
       "      <td>POINT (-1.61098 6.84177)</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "    Region          District             FacilityName           Type  \\\n",
       "0  Ashanti     Offinso North        A.M.E Zion Clinic         Clinic   \n",
       "1  Ashanti  Bekwai Municipal        Abenkyiman Clinic         Clinic   \n",
       "2  Ashanti      Adansi North     Aboabo Health Centre  Health Centre   \n",
       "3  Ashanti     Afigya-Kwabre  Aboabogya Health Centre  Health Centre   \n",
       "4  Ashanti            Kwabre     Aboaso Health Centre  Health Centre   \n",
       "\n",
       "           Town   Ownership  Latitude  Longitude                  geometry  \n",
       "0      Afrancho        CHAG   7.40801   -1.96317  POINT (-1.96317 7.40801)  \n",
       "1  Anwiankwanta     Private   6.46312   -1.58592  POINT (-1.58592 6.46312)  \n",
       "2   Aboabo No 2  Government   6.22393   -1.34982  POINT (-1.34982 6.22393)  \n",
       "3     Aboabogya  Government   6.84177   -1.61098  POINT (-1.61098 6.84177)  \n",
       "4        Aboaso  Government   6.84177   -1.61098  POINT (-1.61098 6.84177)  "
      ]
     },
     "execution_count": 5,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Create a DataFrame with health facilities in Ghana\n",
    "facilities_df = pd.read_csv(\"data/ghana/ghana/health_facilities.csv\") # 데이터 유형 바꿈\n",
    "\n",
    "# Convert the DataFrame to a GeoDataFrame\n",
    "facilities = gpd.GeoDataFrame(facilities_df, geometry=gpd.points_from_xy(facilities_df.Longitude, facilities_df.Latitude))\n",
    "\n",
    "# Set the coordinate reference system (CRS) to EPSG 4326\n",
    "facilities.crs = {'init': 'epsg:4326'} # 공간데이터 프레임 좌표계 지정\n",
    "\n",
    "# View the first five rows of the GeoDataFrame\n",
    "facilities.head()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "pandas.core.frame.DataFrame"
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "type(facilities_df)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<Axes: >"
      ]
     },
     "execution_count": 9,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "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",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "facilities_df.plot() # 그래프가 제대로 안 나옴."
   ]
  },
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Re-projecting\n",
    "Re-projecting refers to the process of changing the CRS. This is done in GeoPandas with the to_crs() method.\n",
    "\n",
    "When plotting multiple GeoDataFrames, it's important that they all use the same CRS. In the code cell below, we change the CRS of the facilities GeoDataFrame to match the CRS of regions before plotting it."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<Axes: >"
      ]
     },
     "execution_count": 10,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "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",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "facilities.plot()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "<Axes: >"
      ]
     },
     "execution_count": 11,
     "metadata": {},
     "output_type": "execute_result"
    },
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAVIAAAHdCAYAAABPM/HiAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjcuMSwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy/bCgiHAAAACXBIWXMAAAsTAAALEwEAmpwYAADnb0lEQVR4nOydZ3gUVReA30kvlDQ6AUJCCEUMIB0jTRCkI4KAiDRpKkU+RBQsIKKCSq8WBBRRepMmhF4CgSA1IZAAKaQSskk2Zb4fYddNsjW7m92EeZ/Hh7gzc+fcKWfOPefccwVRFJGQkJCQKD42lhZAQkJCorQjKVIJCQkJI5EUqYSEhISRSIpUQkJCwkgkRSohISFhJJIilZCQkDASiypSQRB+FAQhXhCEq3ru/7ogCNcEQfhXEIRN5pZPQkJCQh8ES+aRCoIQBDwB1oui2FjHvvWAP4BOoigmC4JQWRTF+JKQU0JCQkIbFrVIRVEMBpJUfxMEwVcQhP2CIIQIgnBcEISAp5vGAMtEUUx+eqykRCUkJKwCa/SRrgbeFUWxOfABsPzp7/6AvyAIJwVBOCMIwisWk1BCQkJCBTtLC6CKIAjlgLbAFkEQFD87Pv3XDqgHdABqAsGCIDwnimJKCYspISEhUQCrUqTkW8gpoigGqtl2HzgrimI2ECkIwi3yFev5EpRPQkJCoghWNbQXRfEx+UpyIICQz/NPN28n3xpFEAQv8of6dywgpoSEhEQBLJ3+9BtwGqgvCMJ9QRBGAUOBUYIgXAb+Bfo83f1vIFEQhGvAP8B0URQTLSG3hISEhCoWTX+SkJCQKAtY1dBeQkJCojQiKVIJCQkJI7FY1N7Ly0usU6eOpU4vISEhYTAhISEJoihWKvy7xRRpnTp1uHDhgqVOLyEhIWEwgiDcU/e7NLSXkJCQMBJJkUpISEgYiaRIJSQkJIxEUqQSEhISRiIpUgkJCQkjkRSphISEhJFIilRCQkLCSCRFKiEhIWEkkiKVkJCQMBJJkUpISEgYiaRIJSQkJIxEUqQSEhISRiIpUgkJCQkjkRSphISEhJFIilRCQkLCSCRFKiEhIWEkkiKVkJCQMBJJkUpIWJC8vDyysrL44osvkFb0Lb1IilRCwkLI5XLatWtHSkoKdnZ2ZGVl8ckXXzFs7s8kPsni5MmTZGZmWlpMCT2QFKmEhIVwcHBg0aJFPHnyhPfff5+8vDzcm/fgxJNKbDx9h3nf/sCKf25zLSKK3NxcS4sroQVJkUpImIm8vDzS0tL4+eefWb58eYGhe0JCAufPn6dSjToMmrOGZJkcgEEtazGtiy8Dmlaj+8TP+eFYFH2nfMWTJ0/UniM+Pl5SslaApEglJMzEihUr+PTTT+nWrRv//vsvR48eBfIV7Pbt27l69Sr7bqWSUPNFtobGAuDu4sCotrVwd3Ggf2BVOrglM6BZDbKyspDJZMq2T506xZYtW5g3bx4//vijJbonoYLFlmOWkCjrDBw4EFdXVwRBoGvXrnh4eHD9+nXee+89duzYAaC0RPsHVi1yvLuLA58MbMOtmAaM+PEs7vf+YflXc9i9ezdeXl48fPiQMVNn8b/fztLoagRtG/uWaP8k/kOySCUkzEBGRgZ9+/YlJimNdaeieKHdS+Tm5pKcnMzixYuV+6laoOqoVq0av92Qcy3FBpvmA3nw4AHXr1+nffv2jBkzhq8PRnAtxYYvdv1bUl2TUINgqZSLF154Qbxw4YJFzi0hURKEh4ez724OCw9FEOTnQWBWGJ6enjz2aqS0QLeGxtLJ35PlwXc58G88DnY2LHytEc/VqKDctuVSDDdj0/i4uz8+Xq4FzhGZkM6CA+FMaFuVvJQY2rZta4muPjMIghAiiuILhX+XhvYSJkPxURYEwcKSWBZRFLl8+TJXrlyhf4/enL+bTHB4Ei26BPEYWHgoQrnvwkMRyu0A2dl5zNxxnZFtaxfYNq2LbxElCuDj5crKIc9z+/ZtVv76q6RILYSkSCW0cu/ePd5++20OHDjA8uXLefLkCdOnT6dDhw7KSPTbb7/NuXPn6N69O0OGDGHkyJGWFtsi3Llzh/T0dCZPnsymTZsIDg4GoFH1CjSuXr6AH1T17+beFYhPyyI8Ph1HOxvm92nAczUqANDJ35MWdRLV+lBVqVevXgGXgUTJIvlI9UQURY4cOcL+/fsRRZHvvvuOmJgYYmJiWLRoEaIo8tNPP7Fr1y7y8vL4+uuvycjIIDIykt9//x1RFFm2bBlHjx4lJyeHyZMnk5OTg0wmIycnp8C58vLySElJAeDo0aOcOnUKURSZMGEC8fHx3Llzh2XLlgFw8eJF7t27B8COHTvIy8sjKSmJ6Ohoo/t85swZTp06xQ8//IBcLmfgwIGMGDGC7OxstmzZgo+PD35+fhw5coTs7Gw+++wzvLy8uHz5MgCPHj0iPDwcgMuXL3Pv3j3kcrnRchUmNzdXGdG+fv068fHxeh338OFDk8qxcuVKYmNj+fLLL3F1dWX06NFsDY1lRfBdnB3ybZaN5+4jk+enKyn8oyHRj7kRl877nX05P/Ml5bC+f2BVfLxctfpQFYiiyMiRI8nOzjZpnyT0Q1KkGoiPj2fevHmIoshnn33GwYMHSZOLHIjK42FiKo6OjsjlcrKysnByciIjI4P69evj7e1NZmYmGRkZPHnyhLS0NJKTk8nIyKBBgwZUqlSJ9PR0fHx8kMvlbNy4UalUe/XqxdmzZ7l9+zYdO3YkMzOTlJQUUlJSyMjIoGPHjtjY2JCTk4OTkxMymYyTJ08SGhpKRkYGv/32G48ePeLEiRMsXLiQnJwcZs6cSVhYGDExMQwePJjc3FzWr1/P4sWLycnJYcCAAVy9epXr16/TqVMnpUKcP38+AM7Ozvj65keDK1asiJubGwBubm7Y2dlhY2ODo6MjAE2bNqVTp06MGDGCzMxMTp48yS+//EJmZibr169n+vTpDB06VO31FkWR9PR0ALKzs8nKylL+rc6Pn5OTQ4UKFXj8+DFbt25lyLgpvLXuDD/89Bvnzp3j+++/59ChQ2RlZVGtWrUCbaSlpZGbm0uvXr0IuXWfwMlrmbPtEv+cOs/cuXPJzc1l3rx57N27V+u0zezsbP7880/u3bvHt99+y8yZM2nXrh3PPfccNjb5r1b/wKpM6+JL/8CqbA2NZXnwPVYE31WmOxXeB/L9pgsPRRTYRxeCIPDyyy/rvb+EaZGCTWoICQnBxsaGs2fPMnz4cC5fvoybmxu7InNZHnyPCUG1mdShrsnOl5eXh42NDXFxcbi7u+PgUNT6UAQVZnT1U+sr08SJEycICAigXLlyhISE0K5dOx4+fEhubi7e3t6Eh4dTo0YNbG1tSUxMpFq1amRkZBRQkMaSnZ3Nzz//jCiK9O3bl9TUVBYvXsyKFSsYMmQI48aNo0GDBrRr145bt26xfPlyoqOjmT9/Pi+99BLffvstnp6e9B44hMmLfyfx/B4cyWbMmDE4ODggCALjNl0mODyJID8P5vdtwOLdFxjYvCYN6npz694DZq3bwydDu9Dy+YY0btyY37buYltoDLce23A6MgWAd1pVprlrMu3bt+fGjRs8fPiQbdu2MXToUKWLQ8G92ER+2HmWPUs/YdCgQYQkOzC1bxsCAuorrcnCVmSyTM7Gc/cRERjWsoZGKzNZJi/QRuH/V7dPSEgIycnJ9O7d2yT3TEI9UrDJAHbv3k27du0YPnw4AM8//zwAYmRk/r+oD6aoe+D1QWG9VKlSReM+Cw6EPw1IhLNyyPN6t92+fXvl3+3atQOgevXqyt/8/PyUf1erVg3It0JNyZMnTwgJCWHlypUAlC9fniFDhpCTk8OUOV+x8kw8H/o7cO3aNXJychg7diyQb3UePnwYyLdYJ3y7nq//vs3YVu3p1sCDDSFxSivOr5Ir2bl5zOjqx9bQWDbfyCImJ5lxDhWYtec+kfb+rD54BZtsGXsPBzNnbzjB4Y8Z0cab7FwREejdvA4+Xo0ACAgIICAgAB8fH3bs2FHEJTFo5vfEVmnNtEUbAfj3UATXMity7ak1CTCqbS2g4HOh+AAny+SsOxWl9llRDPkVbFXTpuI3WVY250+fZEL3ZtKw3oJIilQNH3/8MXfu3EEUxQIR6GEta+DiYKvR8a/ugTcVM7r6AeFP/y1duLq60qFDB+X/Ozs706ZNGwBWnX3E8YhkhAPaPxCCIDCweU1sbe2Uw2TV6PePp6OVke3+LvbKaHd0cgaRiRnUcncCOwdiktI4l5aotF7HtKulbGvS5jCWDnqugMXv4+ODKIoMGzaM48eP07p1a2xsbFg1YwTB0fICz0Inf0/WnbyHm7Md9Sq5KH/XpghVf9OE4hzqglWJKWlcyPHmdo4n7wxoqbUdCfMhDe3VIIoi3bt357PPPuO5557T+7jiWqRlnfPnz7N27VpWrVpVZFtkQjpz992iQdXyjGqnO6gC+dd5w7kHCIi82rgKu6/GIyAytGXNAkPfjefukyyTE5mYiY+nM79deEiLWhWpV9mVyESZMi8zWSZn2E8XiUzMIMjPQ61Cv3jxIjt27GDBggV88sknlC9fnvfee6/APutORSmVo4+nM3smtlbKomtoXlxuREbzZ8gDPhjQHg9X6ZkzN9LQ3gAEQWDv3r1aS5ipexEKD8kk8mnQoAH+/v5qt/l4udLW15OFhyJwd9V9/ZJlcmZuv67MrdxyKYafT0czoo13EX+is4Mdy4PvMa2LrzJSfj4qlfNRqQDsvhpPikzOyYgkRrX15ucz9xn3Ym21523WrBm+vr5s3ryZ2bNnF5j3rqB/YFUepsg4GZHCjK5+rDsVRXPvCqw8fo8ZXf0KKMzCz4ouxappe8zdcMTrJ/Bw7aT1ukmYF51Re0EQfhQEIV4QhKsatg8VBOGKIAhhgiCcEgRBfweelZKUlESPHj1ISMtk3ako5XxoVYoTWX1WGTp0KIGBgRq3F45aa2NraKxyWF6vkgu/n38AwM3YNOV2xX1Rbbdn48r5w3vghVoVGB9UBwGR3y48JCo5k/l/RxCZmMGhGwmM23SZyIT0Iue2sbHh3LlzZGVlqQ3Eubs48EmPAPa/25rbj2QsPBTBrJ03CA5PYsGBcKVftDjPk+r2yIR0xm26TGh0ChtC4njp5R46r5uEedHHIv0ZWAqs17A9EnhJFMVkQRC6A6uBVqYRr2RJTExk7ty5fPPNN3z88cfsCIvX6MdS57eSUM9XX31FbKzmD44hlnz/wKrI5LkIiMzZdZPMnDzsBIhKyiA0OqXAfVFtd2toLFHJmbTxcSPQ241hLWuQIstmW2gMMY/lZObk0cbHjeuxaU+j+EV9tuXLl2fevHnK/9dmRSrkULVItflFdT1PqtsVFnl0cgaRsio0TnHkFb2unoS50KlIRVEMFgShjpbtp1T+9wxQ0wRyWYSMjAw6d+6MXC6nWbNm+OiozDOqbS2t0VdrIDQ6hVk7bzCvdwCB3m5GtVUcv15eXh5r1qxh0qRJRp1bgbuLAy4Otiw8FIGrQ/6AKg94kJrFxM1h7J7QSq1SVtxDmTyXFcF3ERC5+jCNmMdyark7EZWcSf2q5Xn0OBMnOxt6N/kvg0JTvwsrxsL7KeRYOcQtXwYX+wKyFO5XYbk1tacIPL7okc7+lCjGd5PyRy2NqRPyRwH7TNxmiVG+fHlat26t/H/VyjyahmXGDPG1DfVMxaydN4hMzGDWzhtGt1Wcvubl5VG9enVlipc2IhPSGfXrJb4+GK71miiG7HU88tO0fDydcXexI1mWo1Y2hULq5O8JwISg2ogIShfBijeaMK2LLy72Nuz59xGZOXks/idSeW809dvQRHpdlZ5UCY1Ooefys2rbU8yv7xxYl4/6t5SCTFaAyYJNgiB0JF+Rtteyz1hgLECtWtYXlNmyZQvh4eHMnj27wO+RCelM2hxGZGIGgDL9pn9gVfoHViVDnoNMnkuyTG6QVWrOdCkF83oHKC1SYzHEnSGKIqIo0qpVK959913q1Kmj85gFB8I5HZnC6cgU7jxKZ0ZXP47cSqSTvydHbiUqLTOFQurk76mM+A9oWk25T2EU11m1AEj/wKrKVDZ3FwdGebkSmZDOyYgkEtOzae/robw3mvqtaiUmy+TI5LlMCKptEnfPrJ03SJbl4O5ip2xP1UJNkWXz1ZE4elbPpFmhND2JksckilQQhCbAWqC7KIqJmvYTRXE1+T5UXnjhBatbMnHYsGHEp8qKDNUXHAgnMjEDH0/nIjmMo9rWwtnBjoWHInBxsFWbhK1JuZaEnzXQ202ZhmMshvgyw8LCmDlzJn379mXEiBGA7msyo6sf2bm3kOeKyskHweFJnIpI5HRkChnynAIzyvSJ+CfL5GTIcxgfVIeejSvTok6+YlYnx5FbiVx+kKZUtNXcnIsMqzWhmFM/rYuvSVw8ig/gxJfqMHP7deVHRfFBkOeKnIlMJuTCHXp3bG3ySRQShmG0IhUEoRawFXhTFMVbxotkGS5dusRPP/1Evd4TClgiW0NjlSkxihSWwgpQnULUx9osy+lSjRo1onPnzkydOlX5m65r4uPlyro3mxYYireok0hiupzTkSlqZ5Tp+hgp5rcrkvVHebkyfeu/7Lkaz56wWNa9Gajcr5O/JzJ5LhnyHGWbuqZqqspR3JGJOhQfQMXUVwhnft8GSqu6ay0bmlax55upIyQlagXoVKSCIPwGdAC8BEG4D8wB7AFEUVwJzAY8geVPhxc56hJWrZ169erRr18//J/778VUvPjTuviycsjzBQJLo9rWIjIhXWktSFH9gsTGxhIREVHgN32vieoHZtTThHkXextEUCop1doD2j5GqudUKMID1/KrQ92IS2fDuQfK4BWg/NvZwU4ZmJLJc3m3g4/WD4G7i4PakYmxjHuxNtHJGYx7sTbuLg7M79uAvy4+pHZeDPK0JPwqlzfJeSSMQ5+o/Rs6to8GRptMIguQnZ3N0aNH6dChAzY2NsqXoPCLX/hF0jb/3RTWZmmcKRUfH8/cuXNp0KCBsmqUAn2uiaaJDoWVlL61B1SzKxRpQy72kJ2Xv11AVN7fTv6e7L4ar/Rzbjx3X7kP6P4QNPeugI+nM829K2i/SAYQEv2YyMQMQqIfE+jthpuzPXsXTWXdunXUqFHDZOeRMA5pZhP5aU+bNm2iU6eCs0MKv/iFXyRzz38viWCUKXnw4AHx8fE4ODgwbtw4gwMgqsoOMOm1VyTy13J34rkaFQiNTiWonqdyWumotrVYdyqqgJ9zaMuaODv8F+zR9SFYefwekYkZrDx+T5nyZCyF+y0IAl9//TWVK1c2SfsSpuGZn2sfGRmJg4MDbm5uVhf5NKVFWhLW7bp167h8+TJjxowxqEaBgrl7b7LpwkMaV3MlqJ5XgbnzmtA3T7awkp7WxVdj3mbhLAF9MbTUobp7oqsNuVzO0aNH6du3r95ySZgOTXPtn+nCzqIosnv3bvbs2WN1ShQMyzvUhbmntG7ZsgUvLy8WL15cLCUKcCIiX8ndT8liefA9vWTVN09W4V8c/EJ1ark7FRh+K6ZcpsiyGdW2ljI6rnp+fXJ+Ffmdbi72euUHq7snCpfFggPhRfbPysrCzs6OTZs2mWWlAYni80wP7Q8ePEjv3r2pVKmSpUUxO5r8e6ayVJ2cnPRe4kMTX/VtwKydN5jR1Y/bj2R6BeoMyZN1d3HgYUomUcmZrDx+j/l9XdgaGqtMr1L4W4ubhWHovurOo8llkZ2dTbNmzShfvjx//PGH2uLfEpbjmR7aL1iwgJdeegk/Pz/s7OxM+nBqU1DWFERSlH5TN9Q1hBs3biCXy2nSpAlgXX1URXXorLA8R7bxJvzpBABNQ3JD+mOsi0CB4t0cP348kyZNwsnJidq1a+Pq6oqd3TNtA1kMaWivhhkzZtC6dWvefPNN5QJyxUHdsE/bUNqaKkcZUnlJG++88w5Vqxa14LT1sSSmyBZGMfz28XJV9n1Uu1rK3zRhiJtFsa9CUc/cft2gPp4+fZp79+7Ru3dv/vrrL2rVqkWVKlXw8/PD3t6eGTNmcO3aNb3bkzA/z/RnTRRFZDIZa9euxd3dHSieJaVuKKctVcaackxNNSlg3LhxBebT69NHTUPgkrJmVfu+d+9eqlWrRo0aNUhJSdFYP9UQ+gdWVSbQbw2N1XmdJ02axFtvvcWCBQt4/fXX+eyzz2jcuHGRkVKPHj20LksjUfI804r0+PHjLFy4kN9++00Z/W3qXZGtobFFpiOC5hdcndLQpqDK4oymvLw87ty5g5eXF6BfHzUp25JO+xIEgW3btjF9+nRWr15NfHw8b7zxBl5eXtSrV6/Y7SoCXIpnpjCiKPLee+9Rvnx5goKC8Pf3x8PDg+3btyufNZ8cKOxxatGiBXv37qVp06b4+/sjl8tNtlChRPF4phVp27ZtCQgIIFkmZ+LmMJJlOSQ9HYKpm46o6QUvi4rRUFxdXbUGm9Sl9Wi6biVpsSfL5PSb9g3TBg7B39+fjz/+GICJEyfSoUMHgxTpRx99RMWKFZkxY4byN0UfT506xa1btwgKCmLu3LlkZmZSvnx5AgICuHr1Kv/++y/Tpk1THqftY2JjY0NKSgqiKPLnn3+yc+dO1q9fz8OHD6UkfQvxTCvS9957j4kTJ3IkxlZZaWd+nwYaI8bWNCQ3BnMMnV955RUuXryocbshq6AW98Okbx6nov+NXdIISbInvlpb7gr/3dOkpCQaNWpUZIKG6rHqrt2kSZNwdHRk8uTJODo6Mn/+fCZPnszs2bMJCwvD3t6ezMxMGjRoQFBQEO7u7hpdCNqeNRsbG+UKt5UrV6Z58+ZERkYyYMAAzp8/j62trVWm85VlnmlF2q9fP6pXr07/KvkPneLlCFIxQjQV1y3NmGPoPHfuXARBoFq1asTHx9OqVcFFEgqn9RR3jSJt6FLWoijyyiuv0H3qIlafjccj6hjT+rdjWpf6BRSWm5sbu3fvplu3bnh6ehZoQ9O1u3DhAmvXrmXlypXMnTuXixcv8uTJE6pUqYIoirzzzjvKfRs2bKizn/o+a05OTsog38mTJ9mzZw9r1qxh165dkjItQZ5pRfryyy/zxx9/4OjoyKiX1VcZN1bpmHMFyeJiDst61qxZPHz4kGvXrnHjxg3i4uLo3bs3kN/fI7cSmd+3gbK/uq6r6vbCVZg0oW3a6IMHD3BwcKB79+60r27D+hv7mD/5DV5s2bTIvjY2Nuzdu5c9e/awdetWpk+frtymeu0OHz5M06ZN8fDwoHbt2jRr1gyAcuXKERQUpLwu2jDm+VL3HHXu3JnOnTuTnJyMh4eHQe1JFJ9nWpFCfoFpbdXbjVU6+qxpXtKK1RyWtbOzM76+vvj6+uLj48OGDRto2rQp3t7eBmc1FN6ur7JRpDapY+HChTg5OfHll18C8Oust3QWF3/xxRcJDQ0FICoqig0bNjBz5ky61BBxc7Zn8eLFfPbZZ3zyySe4uLjwzTffaG1PHcY8X+qui42NDXK5nObNm3P58mUeP36Mg4ODNDffzDzTCfkKHj16RGRkJI0bNzZ52/pYpKZKircWwsPDiY2NJS4ujkaNGmHn6saxaHmxPxSGfmji4+NxdHSkYsWKJCUl4eHhgSiKZGdn6z3pYuzYsSQkJDBnzhyaNGnC7du32bJlCx999BGtW7dm0aJFtG3bFkEQSEhIUPZVgaHz7ouDtuuSm5uLra0tGzduJCUlpYBVLVF8pIR8Lfz777/89ddfgH5J4oq52eqW7C2MukTuwr+ZKineWvDz86N9+/ZERETwxx9/cPNKCO08ZMW2tvVJhs/IyOCNN94gPT2dfv36cfToUT788EM+//xzkpOT6dChA2PHjmXLli16nbNDhw44OTnh4OBA69atsbe3Z9asWQiCwNmzZ2nXrp3SB+nl5VVAiYL6OfOGTkBQt39odAqvLjtDaHSK1utia2sL5C+FPXHiRD7//HO9+y5hOJJF+pS0tDQEQeCnM/eV1qEm35yianmQn4fOCHRxsLQP1dScPn2aP//8k4ULFyp/U+0jYHR/MzMz+e677/jwww+VCi4iIgIHBwfs7e159OgR9+/fJykpiaFDh+rdbkJCAufOnaNHD8PWjldnkRo68lC3/6vLziiXvTFkCZkbN27QoEEDqZq+kWiySJ95H6mCyZMnU7t2bUZNeA/Q7psztBZmskzOxnP3EREY1rKGTmVhzoT0klbS8fHxPHjwQJmfqUC1j0Cx+puVlYWjoyNvv/02TZs2ZebMmQW2KwpLd+jQgatXr3Lv3j1cXQ0bZnt5eRmsREG9v9ZQf6i6/QsXadH3frq7uxMUFMTZs2f1WtFVwjAkRfqUxYsXk5GRgYtKIKZ/YFXlGj6q6/CoviT61MNUrBsE6LUMhTnzVXUpaVMr2v3793PmzBlq165NixYtePToEevWrWPUhPeBgn00pDKVXC7H39+fsLAwvvjiC63BlCVLlrB69WqDlaipMTTIp27/wosZ6vvRrVSpEm+//TbJyclFUrokjEf6ND3F1dWV7777jtOnTyt/c3dxwMXBVmttTH3qYfYPrMqEoNqMD6qjVBba/GWmrEOqThZt/lhTF1QZPnw4S5YsYdq0abzyyivcuXOHuLg4sp8k6+yjOlnS0tL48ssvsbW15ciRI1SoUIGaNWtqDSI999xzLFmyxCT9sTY6+XsS5OdBJ3/tytHGxoYDBw5gTe60soRkkarQp08ffHx8iIqKUqbG6LIO9amH6e7iUGTevqWWEdFlFZnaGk5ISOB///sfmzdvZuvWrQQGBmJvb0+vXr3Ys2cPVatqdqGokyUzM1M5g0p1Taiy5lfWlyO3EgkOT6JFnURG6cgOmDFjBh9++CHdunVDFEUSEhKeiVq8JYEUbCpEbm4uLVu2ZPfu3SQkJJCamqpMtDYEc8zcKW3ExMTg6enJwoULi/gvv//+e9566y3c3d31vhZHjx7F19cXb2/vItvKWgqZvhjyHImiSGpqKiNHjmTJkiUMGTKEFStWcOfOHW7cuMGbb76JjY0NtWvXLiHpSx9S+pOe2NracvbsWapVq8a9e/cIDw/n0aNH5OXlGdSOriGyOYfv1kBSUhLt2rXjyZMnRZTokydP2LFjB//88w+g+1okJSWRm5vLli1bNM7nt0QKmSXqqRbGkOdIEAScnZ15//33+eijj5g+fTpxcXHIBQdu2dbis68WcufOnRKQuuwhDe3VYGdnx+XLl3F2dqZfv368+OKL/PPPPwaljpSVAieGEhsby7Jly/j000+5ffu2Mp9RlUmTJtGtWzf69++vta0HDx5QtWpVOnbsyMaNG1m8eLHa9sAyFbhKY3aFo6MjL730EvXr18fDwwMHh/wJISfSInijXX8sNUIt7UiKVANxcXGkpKRgb2/P6dOnTVIAQp+XI1kmZ83JKG7GpvFxd3+zzYoxF05OTiQnJyMIgsY0m4ULF2JjY8P58+eV1d8Lc+nSJSZPnsyxY8cIDg6mYsWKZpHXGIVlyewKY1FdzUAhfz2bR1y+fIHy5cvTokULk5+zLCMN7dXwyy+/0KJFC3r27AmgVYmqzjRRRd3QXp+I+NbQWH4+Hc3pyBSjZsWUNKmpqQwcOJC8vDyWLl2qNVfR09OT8uXLM3/+fK5evap2n6ZNm/LLL78AmE2Jwn/3pOfys0XuoS4smV1hShT9CGrdnHr16nHgwAGzn7OsISlSNZw9e1bpE9WlwDSlP6l7EfR5OfoHVmVEG2/a+LgVSPi3pnWe1FGhQgXatm2rd8UhOzs7ZsyYwc2bNzX6n2NjY+nVq5feMhTnY9PJ3xNnexuSZTk6l3Q25Xl1YSkfeqdOnahRowZr1qwp0fOWdqShfSGysrL47rvvyM3NBXQPsTSlP6nz2enjx3N3ceB/LxedMWXNPtfNmzcjk8mYMmWKQcfVqlWLH374QZkDOnr0aLKzs3mSLbI1NJbu/g0YPHgwoijq5VopznD4yK1EMrLzcHex02tJZ1Od15q5ffs2zZs3V/7t5OSkNlNC4j8kRVqIX3/9lczMTEaOHAnoVmCFZ5powxh/nDUXlbaxsaF69eoGH1etWjU2bdrEZ599Rvfu3dm9ezcTJ05k/OKtrL/8mGy5N60aNtTbP12cj43qMcW1/qz5I1ccqlatyuzZs6lSpQrR0dG8/fbbXLhwoUhhFon/kPJIC3Hz5k2qVasGoHXt8MJKUR8laalcR3OWdLt06RL+/v4mm365Z88ePp3/LWPmryOwQiZTJ47lxIkTJmnbWCyZ+1vS587NzcXGxobDhw9z4MABpk+fLuWXIuWR6k39+vVxcXGhXbt2JCUladyvsM9Smw9T4UPr5O9pkgCCoT45dSXdTIEoirz77rvcvHlT5776lh589dVXWTT/c4JXzWbjj6sIDg42lbhFMPQ6WtJPbepz6+q7ra0teXl5XLlyhYkTJ0pKVAfS0F4NdnZ2HDp0iEqVKvHdd9/h4eHBoEGDCuyjUIad/D2VSlL1d1VM7UMztD1Dq1UZgr7WoiGL3ykmRPTt29eslYoMvY6WHMKb+tz69P3Ro0dcuHChwOqmEuqRhvY6iIiIoHz58iQkJODh4UGFChUKbNc0XDd1vU1VrGF6aWxsLC+99BJhYWE4OOh2bZRExXhDsYbraCn06bsoijg5OWmcBPEsomloLylSPRk3bhyvv/469Zs008s3+izM/f7333+VAQhT9PdZVmzWhOI++NnE89vPa1m/fr2lRbIapMLORpCSksLixYvJyclRKgzQPhwsa5FcVS5dusRvv/3G119/rfzNFP0ta2lEpRXFfZjWxZcPPvjA0uKUCiRFqgOZTEabNm04fPgwbm5uRRSGthVBi1M4uTRYZcnJydSvX7/Ab6ZIzyrLH5/ShOL6p4cdxGf0CMsKU0qQFKkO5HI5Z8+eVaZCFVYYuhSrAoWClMlzWRF8t8h2BRvP3Wd58D0y5DlFapgWxhJK98CBA8yePZszZ86YvG1rzpV9llDch9/uuRAVFSXlj+qBlP6khcjISJ5//nmt+aSaVgTt5O/JkqORLD16R6nwFh6KQEDUmgIlIhT4VxuWSMfx9/enX79+BpcVtFasvYZBYUpS3qFDh0ozmvREski14OPjw61bt5TTRfVBoVjXnYpSWp7ODnZ6z6AZ1rIGLg62yrQqbftbYihcp04dqlevzquvvsq+ffv0OkbfqleWcGmUNr9sSZbuO3nyJAsWLJCKmOiBpEh1sG3bNmrXrs3zzxfNfdT28isWzhMQldv1efBVFbGuF8bcQ2FN/Rs8eDBdunThww8/ZM6cOTrrtOrz8ltKoZU2v2xJlu5r06YN+/fvN/l5yiKSItWBnZ0d9vb2ardpe/ndXRx4t4NPkWP0tbxK+gVXJ5em/tnb21OtWjXq16/PiRMnCA8PZ/z48Rrb1qcvllJopc0va055C9+DqVOnMm7cOAIDA81yvrKElEeqA5lMRk5Ojlo/aXGGo6bMLzXlcFidXPq0HxkZyXvvvceuXbuMOr+E9XH58mXq1q3L7du3adeunUmKm5d2pLn2xWTo0KEa53sXp2akKQv2mjLYpE4uffrn4+PDe++9R2RkpNEySFgXzz//PGlpaaxYsYLc3FzS07XXSXiWkRSpDrZs2UK7du2KfXyyTF4gem9KTKmUDfkofPDBB0RHR7N06VLu3r3Lrl272LFjh9EySFgf1atXZ82aNaSkpNCxY0ciIiIsLZJVIvlIdXDw4EHc3d1p0qRJsY7fcO6BMnq/KyyOXs9VYXnwPaCoX1UxlO7k78mRW4k6h+yW8u+1a9cOJycnQkNDqVu3Lj/88IM07CvjuLi4cPz4cRwdHS0tilUiWaQ6kMvlpKamFvld33w+gf980NHJmYgIGq1IxVD9i323WXgogg/++pdkmdxqch137drF119/jYeHB9u2bSMqKooePXpYpRK1lmtW2gkODmbQoEFkZGRIVaC0IClSHfTp04eGDRsW+V1f/+TQljVpWyd/8bY2dSoyrGWNAkNo1RdeMVRvUDW/OtLpyBS2hsZaxXpNMTExrFu3ju7duxMXF0e/fv346aefTH4eUylAa7hmpZ2cnBx27drFu+++i7u7OwEBxVuK5VlAGtrrQffu3Tl69CguLi7K3/RN13F3cWDt8GYatxdOMVLM13eyt1PmoBY+Z0nz+++/s2XLFrZv3w7Ac889Z7ZzmSqftLTlh1ojubm5BAQE0LVrVwAmTZpkYYmsF53pT4Ig/Aj0BOJFUWysZnsA8BPQDJgliuK3+py4tKQ/ASQlJWFnZ0dubq7JfUSloUhJXl4e0dHRJVIlvSSvR2m49pYmLi6OatWq4eTkZGlRrAJj0p9+Bl7Rsj0JeA/QS4GWRjw8PNi3bx8fffSRydu21LK7hhATE8PcuXNL5FwleT0MGf4/qz7XqVOn6rWUzLOOzqG9KIrBgiDU0bI9HogXBOFVUwpmbfTq1YuePXtaWgyLkJWVZdbhvKUwZPhf2ubkm4r169fz+PFjUlNTqVixoqXFsVpKNNgkCMJYQRAuCIJw4dGjRyV5aqOJjIxkw4YNFpXBUlZRYmIikZGRZGZmluh5zY0h1q8pc3a1oe8igSWFnZ0d/fr14/jx45YWxaopUUUqiuJqURRfEEXxhUqVKpXkqY3G1dW1QLBJE+ZUdiUViS7cB09PTzp37vxM+8mK43IojlI014qvxeGrr75i8+bNnDt37pkdjemLFLXXg4yMDPLy8hgwYIDOfYs7BNQn8FFSkejCfahbty6bN2+mSpUqtGjRwqznLksYsnKqAnOu+GooY8aMwdXVOhYqtHYkRaoHCQkJjBw5kr179+rct7jKTh8FXFIzmdT1ISoqioyMDLOf25oxNMpfHKXo4+Wqt9I1N2lpacjlcjw8PCwtitWjT/rTb0AHwAuIA+YA9gCiKK4UBKEqcAGoAOQBT4CGoig+1tZuaUp/gvwZTjk5OWZr39pTcY4cOcKuXbv47rvvLC2KURhznZ+FlWFVWb58OefPn2fz5s2WFsVqKPYqoqIovqFjeyxQ0wjZSgV//fUXqampDB8+3Kh2NL3I1l4X08PDg9Lm11aHMdH3ZyHJX/X5HD9+PFOmTLG0SKUCaWivJ82bNyc7O1vrPvpYO6U1jea5556jceMi8zFKHZqUoT73zto/dqZA9fm8s2cVnTp1olevXhaWyvqR5trrib+/Pw0aNGDKlCnI5XK1EVl9ouollUajICwsjMTERGQymbLU3a1btwgLCwPg/PnzPH78GLlczp07dwB4+PAhMTExyr+zs7Pp0qULK1asKBGZzYmm6Ls0Nz8f1efzf//7Hy1btrS0SKUCSZHqiUwm4/Hjx7Rs2RJRFNWmqeijJE0xc0cURWQyGQCnTp0iJCQEURTJysoqst9XX33F/v37SU9P5+uvvwZg7969rF27FoCPPvqIc+fOcevWLfr06YONjQ2LFy9mwYIFCILAyy+/zIULFzh48CDjxo0rtszWTkl/4KwVxfO5dOECUlJSqFKliqVFKhVIS43oyeuvv87o0aNp3749kJ8juOBAfkTWx0tzikhxgxvp6em4uroSHh5OYmIirVq1YsqUKQwePBgXFxf69OnDnTt3+O6778jIyCAuLo6srCxWrlypbCMmJoZq1aoVv9MSzyz79+/n5Zdfpnz58pYWxaoodrBJIp/169cXWJZZ3zQVTT5RURQRBIGYmBhu3brFSy+9xOeff87zzz9P9+7dqVWrFlFRUZw7d44jR47QqlUrAgMDcXNzo379+oSH51vCimBAWloazs7OLF68GC8vL3r37k3Lli25evWqyaf2WXuGQWnD2q6nTCbj77//pkePHpYWpdQgDe31YMyYMdy5c8egAsaK2UGd/D2VQ8b09HTi4uIQRZFGjRoREhLCrVu3lClFgYGB1KxZEwcHBx49eoSrqytDhgxRDsPfeust6tevD4CNTcFbV758eezs7Khbty4+Pj6UK1eOa9eumWV+tORPNC0lcT0NmXHn4uJCt27ddAZXJf5Dskh1IIoi/fv3p2ZNwzK8VC3R+nlRVHSqyXffLeHkyZNs3bqVXbt24evrC8BLL70EQO/evZXHF1aU+qI6lc9cw7JnIQ2oJDHn9VRYuxnyHI1L3BTmzp07vPLKK3pNiZbIR7JIdbB3717s7e3VLsesiWSZnAx5DuOD6tA/sCq//PILu3fvZtq0aWzduhVAqURLI9ZW+s/aCn0Yijmvp+KDrm2Jm8L8fvAsbefuI+RuosnlKatIFqkOqlevrvRH6svW0FiWB99jWhdf3F0cWLNmjZmkk4DizWkvrRjqT1W1dvVV1CdzfEjJy2DYkgNsGOJP8+bNjZL5WUBSpDoIDAzE39/foGMKD9Xef/99hrw9lquy8lYTUChLWFOhD3Nj6ISO4kwimNc7gFk7bzCiQTm8vb2LJeezhjS018HKlSv58MMPC/ymy3Hv7uLAyDbePEmMA8DR0ZHQVCcpQGMmFBkU2tLQ1KHtPlprRfySyHcN9HZjz8TW9H+pGb6+vly7ds1s5yorSBapDsaNG0dWVhbLli2ja9eu+Pr6arUK/v33X2xtbalbty4vvvgihw4d4uuvvyZZJsfO3l4K0FgR2u6jYtv5u8nM79vAakYRJTlN1dbWlgULFrBr1y61K+lK/IdkkepAEAScnJzw8fHh8ePH3L9/v4hVkJWVxbZt2xBFkX379rF69WocHBy4e/cufn75w01rC9CUFsxpGWqz7voHViXIz4Pg8KRnehTRs2dPMjMziYyMtLQoVo2kSPWkf//+HD9+nAkTJhAfHUmVpCs8SYxj9OjRODg4sHTpUsLDw/nggw9YtGiRxnasdchorRiaY2nI9Q178JgfT90j7EHRio/uLg7M79tA6zBa17nKwr329PTk7t272NraWloUq0ZSpHogl8sZOXIknTt3ZsuWLYSGhnL8+HHu3btHnTp1EASBw4cPU69ePZ1tScnshmGoT9CQ6ztzx3WSZTnM3HFd7XZdowhd57LU0jCmbCMsLIyIiAjc3NyMlLJsI8211wOZTMbt27fx9fUtdqK8AmubDmhKrKFvumRQ3R724DEzd1xnfp8GBNXzMuu5zHk9TFFwWtlG57oMfM6dChUqKLfNmzcPT09Ppk6daiqRSy3SXHsjOHXqFM7OzkYrUSjbNS2todaqrutbWMaTH7xotnNZcmkYfbl79y55eXm0rWrH9RpyejbyIjDwOaKiovjuu++oXbs2X3zxBXl5eaYWu0whKVI9SE1NNYkSNQZrsPZ0oc8Lbel+lMXprcYo7OTkZHbu3En79u15ta4DPtUrERMTgyAIvPbaa7i4uGBjY2Px59/aka6OHgwYMAB/f39CQkIK/F6SwQRr862q67s+mQkl3Q9N9yhFll3qckhNTbJMzsUMT/73yef06tWLV199FUBZnMfX11cqw6gnkiLVk0uXLnHx4kXy8vJQ+JVLUilYW+Hh4va9f2BVJgTVRibPLRFFteHcAxYeimDDuQfAf3IvOBCuUX5r+2iZC0U/X313LqIoMmLECH744QdLi1UqkYb2eqJYt+bw4cMsWrSILVu2lOgw0dp8q8Xtu7uLA84Odiw8FIGLg63BfTLUNSAgFvhXIW8nf09a1EnUmEOq+m9ZRdE/H9GVjIwMJk+eTFZWFkuXLmXUqFE4OzsjiiI5OTnY2dmRnp7OtWvXSE9Pp2PHjhaW3rqQFKmBdOrUSbmOjYutaFXKrSRRKHbFMNgQn6cxikpXQKuwoh3asibODnbKc6l+kEZpmFJqbR8tc+Hu4kBW6G5sGjYkMzOTO3fu4OnpiZ2dHQ4ODqxbt47nn3+eYcOG8dlnn7Fz506mTJnC1atXOXPmDA0bNiQ5OZnu3bs/80uSSIrUQARBoHz58nz88cd4eXkxduzYYrVj6aCLqShOpN4YRaVLCReW51lRisVlwIABREZGkp6ezqeffsqVK1eU9XHj4+Nxd3fn5MmTODo68uqrr2JjY0PDhg159OgRCQkJxMbGcvToUQYNGmThnlgWSZEWk5kzZxpUMb8w2hRQaVKyJT0M1qUYVeXRdB2TZXLWnIziZmwaH3f3N7jYibURGp3CrJ03mNc7gEBvN4OOTUhIICMjA29vb65cuQLkFzO/evUqNjY2GoNNlSpVolKlStSoUYMWLVrw+uuvG/U+lHakYFMxOXTokHL54uKgLXikLthhrZFka6shoCqPpqDR1tBYfj4dzenIFL7Yd9tCkpqO6duuE5mYwfRt6mdoaSMzM5PU1FSSkpKQy+WcOHGC3r174+bmRtWquj+O9+/f5/nnn+fGjRvcv3+/OOKXCSSLtJjEx8fr9aBpQptlpc7Ks4Zkd2slWSZn47n7iAgMa1lDqdQ1Wcud/D3ZcCaKuCfZVK9g+Q+AsSMQL1d7HqRk4uVqb/CxzZs3p3nz5nTt2pXBgwcTEhKinMk0cOBAncdXq1aNd955h++++46AgADmz59PfHz8M2edSlNEi4koiqSkpODo6Fgi5ytNw/2SRjG9EdBrmuSSo5GsCL4LgKOdwE9vBho8JDYlxk7x1HdpcG0kJSVRoUIFbG1ttSpBbc9hbm4uZ8+epVOnTmU2gV/TFNGy2dsSIDs7mxdeeIG0tLQSOZ+1DaGtCUVuqmKNLNDuColLzVD+nZUj8s6mKyZzmehbESoyIV25n7E5wsUtbK2Kh4cHdnZ2Oi1JbTm2tra2yOVyXnvttWLLUVqRhvbFxMHBgYsXL2Jvb/hwSsK0uLs4MKlD3QK/aXOFXIgqWDYvLSuXmduvm6SAsy4XjGrB6Px1pvL3Ky3uGk3uEoWl2rtZK378sa0lRLMokiI1guDgYJo1a4a7u7ulRZEohLZsgq/6BjB202WeZOXhYAP+VcsrCzgbq9B0ZTHoMyEArNeVo8m3r/hAxMXGMuh5z2eu7J6kSItJXl4eu3btklZYtFK0BfMCvd34+902zNx+neDwJF7086Rbw8omSeEypCKUpgkBUPqCi4prl/HvEeLj61tYmpJHCjYVg3Xr1tGqVSvq1q2re2cJq0Vh9XXy9+TIrUSl9WcN1qA1yFAc0tPTkcvl1KhRw9KimAUp2GQiRFGkatWqJCYmWloUCSNRWIdHbiUWCKBYQ9ESQ4KL1pJjnJKSwvbt21mzZo1F5bAE0tDeQP7++2+ysrKkog1liMJ+zdJWtMTcbgB90qvkcjl16tThzp071KxZ0+QyWDuSIjUQd3d3HB0defjwIU+ePMHf39/SIkkYSWG/Zmmbn28Kxa/NlbDgQPjTDINwVg55vsixcXFxVK9enSdPnuDgUHrcEKZEGtobSKtWrejQoQO///670VPi9l6Npfn8Y+y9WrbrXpY01jLULSlMkWOszZ0xo6sfQX4ezOjqp/bYFi1aMHfu3GdWiYIUbDIKURT5/vvv6dWrF9WrVzf4+Obzj5GRnYezvQ0hM18yg4T6UVoDG5owxWJwxcGY4iGaKKl7Y8x58vLysLOzw8nJyUzSWQ9SsMkMCIKAj48PSUlJxTr+i171cba34Ytelk0XsYbgiimx1GoCs3beIDIxg1k7bxT43RgLuaTujbFW7cSJE4mJiTGxVKUHyUdaTM6dO8cPP/zA2rVrKa5V36NxVXo0tnxAo7QFV3RhKR/nvN4BSotUleIGg5JlcjLkOQWmvlojNjY2dO/eHS8vw5e0LitIFqmBJCcnM3fuXAICAvjwww+LrUStibIwj98a/KKB3m7smdi6yLC+uBby1tBYlgffw8XB1qrujbprff36dRYtWmRBqSyLpEgNZPPmzXzyySfY2dnh6+traXEknmLN7onifqj0VcCm/ojoak/dtZ4yZQrjx483yflLI9LQ3kBq1arFzp07LS2GRCHKmnsC9HdRmDqPVFd7qtc6NzeXzZs3ExERwfz5840+d2lFUqQGcPbsWTp27FgmhvPmpqQzAUpb7qcpMfVHRFd7imudkZFBq1ZtOXnyJLm5uSY5d2lFGtrrwQ8//EBMTAzz5s3j33//tbQ4pQJzDLWtwQ9qjZjax61ve46OjgwfPhx3d/dnOtAEkkWqF87OzsTGxvL7779bWpRSgzmG2tZYEclacnAtIcfjx4+5ePFiiZzL2pEUqR44Oztz+PBh6td/9sqDFRdzDLWt0Q9qLcrdEnIcPnyYjz/+uETOZe3oVKSCIPwI9ATiRVFsrGa7APwA9ABkwAhRFMvUZyooKAiZTGZpMUoEa7Gw1GGNflBrUe6WkCMxMREXF5cSO581o4+P9GfgFS3buwP1nv43FlhhvFjWRVxcHJmZmZYWo0Sw5jQia8RacnBLWo579+7RrFkz/PzUz79/1tCpSEVRDAa0zYHsA6wX8zkDuAmCUM1UAloDYWFhnDlzxtJilAid/D0J8vOgk7+npUWRKCGKE8TLyMhg7969ZpSqdGGKqH0NIFrl/+8//a1MEBMTw+7du+nZs6elRSkRjtxKJDg8iSO3pMLVzwrFGYV4e3tTsWJFM0pVuijR9CdBEMYKgnBBEIQLjx49KslTF5uqVasyY8YMHjx4gKJa1ePHj7Ueo+4LHxqdwqvLzhAanWJOcY0iWSZHJs9lQlBti/v8JDRj6jSw4kxhjY6OpnLlyiY5f1nAFIr0AeCt8v81n/5WBFEUV4ui+IIoii9UqlTJBKc2P4Ig0Lp1a1JTUwkJCeHYsWM0bdqUNWvWYGNjwwcffIBcXvCBVveF11QZyJrYGhrLiuC7ODvYWdznJ6GZws+XsYq1OP7VgIAAunXrRnh4eLHOWdYwRfrTTmCSIAi/A62AVFEUy1w9rW7dutGtWzdEUSQkJITr16/j5OREnTp1yMzMLFDUVl0EVVNlIGvCWiLQEtopfJ/MkfqkT/bG3r17ycrK4t133zXJOUszOgs7C4LwG9AB8ALigDmAPYAoiiufpj8tJT+yLwPeFkVRZ8XmslDYGWD//v3UqlWLOnXqWFoUiWcUc6Ss6Vsc29bWFgcHB/LVQNlHU2FnnRapKIpv6NguAhONkK1UExcXh62traRIJSyGJSc/hISEMGfOHA4ePGjS85c2pKVGTMCzkqwvIaGOhw8f4uXlhZubm6VFMTvSUiNm4tGjR3Tv3l2qCCXxzJKQkMCQIUMsLYZFkRSpkVSqVIlly5Yhl8uf+eGNxLNJkyZNWLx48TNdSk9SpCagcePG5OTksGfPHkuLIiFR4oiiyBdffMHhw4ctLYrFkBSpifDy8uL777+3tBgSEsWmuPmogiAwYMAAWrVqZSbJrB9JkZqInTt38tFHH1laDIlnCE2KT5tC1LatuAVr/v33X8LCwrh586ZhHShDSPVITUSXLl14/vnnEUXxmcmpk7AsmhLxtSXoa9tWnAkZMTExfPjhhxw8eBAbm2fXLpMUqYlwdXUlIyODsWPHsmrVqmf6oZIoGTQpPm0KUdu24uSjVqtWjQ0bNjzzxoOUR2pCcnNz2bZtG506dcLJycnS4khImIWTJ08il8vx8fEhJyeHd999lwMHDuDo6Ghp0cxOsWc2SejHkiVLePLkCe+//76lRZGQMCmXL1/mzz//JC0tjRYtWnDr1i0qV67MunXr+PTTT/nnn3+e+RGYZJGaiLS0NA4cOMD58+eZOXMm9vb2lhbJKohMSGfBgXBmdPXDx8uV7OxsAOn6WIi8vDy+++47Ro0apfdMpNjYWJKTkwkICMDR0ZFNmzbRokULfHx8nrmRlzSzycyUL1+eDh060LdvX7799lvJMn3KVwfCCQ5PYvDSf0hKz2LEiBHMnj2blJQUdu7cKU2vNQPaIvO3b98mNDSUJ0+ecO3aNZYtWwZATk6OxvauXr3Knj17eOedd7Czs2P48OE0aNDgmVOi2pAUqQnx9PTE19eXuLg4Xn/9dd577z2ioqIsLZbF2Lp1K/ZhO6nslEeajSvbQmNZu3YtX375JQ8ePOD3338nNzeXrKwsg9u2xjXurUUmbWlMT548Ye/evbz33ns4OjqSnJzM4cOH6devH0+ePGHPnj3MmTOHxMRE0tLSmDNnDsuXL6d58+bSiqFakBSpialQoQKdOnWiXbt2vPjii/z888+IovhMzsWvWbMm3doGsm3Si/kV2JtWw9nZGUEQaNSoEZs2beLmzZt07NiRU6dOGdS2NS7SZy0yaat437x5c/755x9GjBiBr68vH3/8MTVr1mTu3Lm8/fbbBAcH4+Pjw7Rp0/j+++9JSkpi0aJF7Nq1i6tXr1qgN6UDyUdqBjIzM5XFnkVRZMiQIdSvX59PP/3U0qKVGI8ePeL8+fP06NFD574ymYy0tDTWrFmDu7s7EyfqrspoSA3Oklpi2pqXslZlwYIFtG/fnnbt2hX4PSkpiYoVK2Jra4tcLsfW1pavvvqKVq1a0bFjRxwdHbG3t3+mU50kH2kJMnz4cI4cOQLkT59bu3Yt48aNs7BUpiMlJYXERO2L48XFxbFx40a92nNxccHDw4MmTZowcOBArl69qgxKacKQ5TFKylI055LIpnIbpKSkUKNufY7F2Rdpy8PDA1tbWwAcHBywtbVl1qxZdOnSBVtbW8LCwvT6MD6LSIrUDGzcuJFXXnlF+f+urq5s2LCBVatWWVAq41G4KNavX8+IESPIzc0lLS2N3NxcMjMzSU1NBeDw4cOUK1eOr7/+Wu+27e3t6d27N15eXkyfPp3z58+Tm5urdIncu3cPyC/ZlpCQYJDcxVnczdowxcdg3rx5/PLLL2TVaMaGq+kGtxUQEMB33333TLqpdCEpUjMQGxvLjh07CvzWt29fatasaSGJ9CM3N5eMjAyN22/cuMGbb77JhAkT2LVrF1u2bKF169Y8fPiQOXPmMGTIEGJiYli9ejVxcXHUqGH4qtw2Njbs3LmTVq1a8dprr/H++++TmJhIjx49kMvljBgxgtWrVxvUpjktxZJC18cgMiGdcZsuE5mQrrGN1157jVdeeaXYHxZBEKhVqxYzZ87k/v37Bh1b1pF8pGYgIiKC7du3M378+AK/nzx5krNnzzJu3DhcXFwsJF1RDhw4QJs2bRg6dChdu3Zl1KhRPHz4EF9f3wL7paWl8dlnn/Htt9+WiFyiKJKVlUVUVBTOzs54e3sTFxeHl5eXcggqkc+4TZcJDk8iyM+DlUOeL7I9Ozub1atXM27cOKOv3Z49e+jYsSNeXl5GtVMakXykJYivr6/alRXbtGnDvXv3rMpZL5PJ+PLLLxFFka1btzJp0iQ2b97M22+/TVZWFnl5eQBs376d77//vsSUKORbQE5OTvj7++Ptnb/i96effsrevXtLTIbSwoyufgT5eTCjq5/a7fb29uzatYs7d+4Yfa5XX32V5ORkjh07ZnRbZQVJkZqJAQMGcPfu3QK/2djY8MMPP7BkyRJ69+6tM6BibkRRJDc3ly1btlChQgXs7PJnDI8YMYLg4GAOHjxImzZtCAsL48GDB7Rt29ai8gKMGTOGTp06WVoMq8PHy5WVQ57Hx8tV7XZRFNm/fz8VKlTgzTffJCUlBSh+ECs6OpqLFy8+s6l9hZGG9mbi+vXrVKlSRe3sj7y8PIKDg2nQoAFVqlSxgHT53Lx5k+HDh3P27FmN+6SlpeHg4GA1BSlWr15Nt27dqF27tqVFKTV8/vnnZGZm8vbbb3PlyhVsbGx46aWX8PDw0HvZZXVkZ2fj5uZGWloa5cqVM5P01oU0tC9h3N3defTokdptNjY2NGjQgEGDBun9NTfHrBlfX1+mTJmidZ/y5ctbjRKF/IDYpk2bLC1GqUEURfr06cPo0aO5evUq0dHR9OvXDw8PD8CwjIbCz6C9vT2JiYmsWbOGvXv3kpqaqnQFPWtIitRM7N69W6svr3Llynz++ed6t2eOXMjVq1fTvXt3k7VXEgwfPlyt/9kasOQUUU3nXrt2LQB169alX79+TJ48ucB2Y/NxnZycqFq1Kv7+/syePZvdu3cb35lSiKRIzcTo0aOLRO1VEQSB3NxcunXrptFyVcUcuZBnzpwhMzPTZO2VBMuXL+ePP/6wtBhFSJbJmfbXNRYeimDDuQclfn5NH1q5XM6JEydMcg5Nz2CfPn2oXr06H3zwAV26dOHrr7/W65kuS0j1SM1EdnY2kydPZuHChRqj9B07dsTe3p6EhAQOHjzIwIEDNZaXK071cm2Iosg777xD5cqVTdamuRk8eDA9e/bkpZdesrQoRdgaGsuZyGQABEo+7qCu8n1MTAxvvPEG7u7uJjmHrmfQ09MTyJ8inZSURHp6OnXq1DHJua0dySI1E/b29jRv3lxreTKA9u3bU61aNXbv3l2iJeUiIyOZMWOGVaViaSMjI4OsrCx69OihTIWyJvoHVmVCUG3GB9VhaMuSn3ihbogeExNDx44dS9w6/OCDD7h+/Tpr1qxBLpfz1ltvkZaWVqIylDRS1N6M5Obm8vjxY72CNe+//z7ffvttiRY8vnDhAs2bN7d6ZfrJJ59w5swZXF1d2bZtm9XLa00kJibi5OREbGxskQkWmihcjNsYcnNzOXbsGH5+flSrVo2KFSsa1Z6lkaL2FuDHH3/kk08+0bnfkydPuHXrFrm5uSUg1X8kJiZy+PDhEj2noaxatYpLly6xceNG5s+fLylRA6lYsSLffvstY8aM0fv5WvC0GPeCA+FGn9/W1pZOnTrx4Ycf8vDhQ6Pbs1YkRWpG3nrrLRYuXKhzv3LlyjF//nwiIyNLQKr/cHR05Pfffy/RcxrC2LFj8fT0ZNWqVVSuXJkGDRroPMZaiisbS3H6UXi+fV5eHm3atGHQoEEcOnRI76mhumZJFYf169dbfa0JY5AUqRlxcHDgzz//1KtK/o4dO/jss89KQKp8cnNz8fHx4fvvvy+xc+pLUlISTZs2pVOnTjRq1Mig4ifWUlzZWHT1Q52iLWxJ2tjYsGLFCurXr2/Q4nS6ZkkVh6NHjzJy5EiTtWdtSFF7M5Oenq5XitGcOXMQRZHPP/+c0aNHU716dbPKdeHCBWbNmsWBAwfMep7ikJiYyDvvvMOgQYMMHsprW7e9NKGrHwpFCygj6fkWZHgBS/L7779n5syZNGrUyLwC66Bdu3a0adMGURTLpHtGskjNzJgxY/D399drX0EQSE5OLpG5y40aNWLXrl1WuYzu3r17+eWXX4qVxVAWSuaB7n6oy+lUZ0n26dNH64c8IiJCZ2aJKThz5gxTp04lKyuLq1evlng8wNxY31tUxrh8+TKDBw/We//PPvuM6OhokpKSzCgVvPTSS8TExJj1HMWlXr16NGrUyKqmplob+n4wypcvj1xe0M967do1fvjhB2JiYhgxYgQ3btzQ65za/La6fLodO3Zk1apV3Lt3jylTplh9kNNQJEVqZgICAvjhhx/03r98+fKsX7+e7du3m08o4JtvvlFWe7I2atSogaenp9XKV5oIj45l7p9niEtJp3///ly5coVTp05ha2tLtWrVOH78OKmpqVy5ckVnW9r8tvr6pr29vRk4cCBXrlwpU1WjJEVqZpycnHjw4EERq0ATgiCwfPlys1c3CgwMNNnUQVPTpEkT9u7dq7elVBqwVDaBc6NORFYMZMOpcMaOHUvdunUZPXo0kyZNUu6TkpLCTz/9pLOtwu4E1T4ZMoV52LBh/PXXXxw9erTY/bI2JEVaAqxevZoHDwybfz158mTCwsJ07lfcF9TOzo7du3cXqZlqDKZSFhcvXuSdd97R27dcGjB1NkFSUhIDBw7UueyKQsGN7NCAV155pUi5u6SkJERR5Lvvvivwu7p7WdidoNonQ33T8+fPlxSphGH88ssv+Pj4GHTMoUOH9Iq0FvcFrVChAhs3biQnJ0c5xEpNTSUrK4uIiIhilUMzlbK4efMm8fHxJRIIO3HiBHFxcWRkZLBt2zYgf20qU35gwDRFZ0RR5ODBg0yePJnly5czdepU5Rr0ioUHC6NJwX388cccO3aMf/75R61C0+deGtOnypUr4+XlpfdIzdqRFGkJcP36dYNK5kH+F/vPP//UuZ+xL+jkyZOZN28ef//9N4MGDSI3N5eZM2fqrFNqDlkA9u/fz6pVqwy+XoZy+vRpjhw5wjfffENMTAwbN25k165diKLIl19+yT///IMoikU+KMW1ujUpNEPaO3fuHMuXL2fo0KF07NiRNm3a4OjoSIMGDcjNzTUoJzgzM5M6deowYMAAvv322yJy6HMvjcmQqFOnjnJts7KANNe+BHj06BEXL17kxRdf1PuYs2fP4uPjY/bqTHl5edjY2JCRkYFMJsPT0xNRFLlw4QJ79+5lzpw5Zj1/YX7++Wdq1apl9uVE1m/eyo9Hr7Nt4XSNimDixInExMSwdetW5W/GVJRXhyHtZWZmcvT0BR441qJ/YNUiBUpmzpzJZ599xr179wgKCtLaVlpaGjY2Nri6upqlX/qQlpZW6oKK0lx7C+Ll5UWbNm0MOsbb25sxY8aojWyaMnChGD47Ozsry6AJgkDdunU5d+4c0dHRRp/DEJ48ecKyZcvMfp7MaoHEV2urdei6ZMkSli1bxqlTp1i8eDEZGRkmrwtrSHtffvklYU9c1Q65q1Wrxk8//UR8fDyrVq0iMTGR8ePH888//6ht6+bNm7Rv375YchhDWFgYU6ZMQRAEpk6dWmYW0JMs0hIgIyOD6tWrGxRwys3NZeXKlbz66qucOXOG3r17K5dwLinrQRRFkpOTlctSlARpaWkkJSWZPWuhwfMvMOarn3j7pfo6h6bR0dEsXbqUuXPnApRYha5kmZytobEEOCRz80oIqamp1G3QhIQK9YpYpIWRy+V88sknDB48mOeee06t1ZeSkoKbm5vRcoZGpzBr5w3m9Q4g0FtzewcPHsTPz4/k5GTat2/PzZs38fX1lSxSCf1wdnYmLi7OoGNsbW2ZOHEinp6enDp1iu3bt+f7spKTTWY9rFmzhpCQEAC1s4jy8vJo3Lhxic5CKV++PB9++CFnzpwp1vFLly7l0qVLBX6Ty+XKyQfLly8nPDyct954jfdebqCXf8/b25sFCxZw9epVWrRoYZIljTWhOtpQBHzWHr5KRkYGU6ZMoc8rnfXySzo4OLBgwQLOnz9P//79i2z/7rvvMJUhM2vnDSITM5i4OUztKOnOnTvcunWL69evY29vr7SE69evX6qUqDYkRVpC/P777zx58sTg43JsHXl+8Af4P9eU2NhYypUrZ5JpkHl5edy5cwcbGxt+/PFHGjVqVGAqoWKp5i+++ELvqkGmYvjw4TRs2NDg40RR5MyZM9jZ2TF9+nR27drFpk2b+O233xg4cCB3794lKioKR0dHPvzwQ4Nf4qZNm3Lo0CHKlSvH6dOnTTK1ct26dRw+fFgZdVeNlvdtUoUXyyfwZvt6TJw4sVjtV6lShQ8++ID4+HiuXbvGCy+8QFRUFNWrV1e6coxlXu8A3F3sSJblqHWVxMbG8vfff/Phhx+WqZQ2VSRFWkLcuHGjWIpU8WJdTnPlpZdeYtSoUSQkJBglS3x8PO+//z7z58+nadOm9Bs8jJnrD3P+ynVee+01Ll++zJ49e5g8eTKjRo0y6lzFoXv37mzdupVx48aRnZ2t93Fz585lypQpeHl50bhxY7Kzszly5AhDhgzhxIkT1KlTh6+++qpYFfYVJerScMbBwYF58+YZbJn27t2bv//+m+3bt9O5c2cSExM5deoUlStXpmXLloSEhOAjxjK+TVVs7p5lweef0DegHFG3rxksL+R/WJYuXcr777/PBx98wOXLl9m1axe1atVi0KBBNG3atFjtFibQ243dE1qpHSXt378fW1tbZsyYYZJzWStlw64uBXzxxRdkZWUZfJxqFSB3l1pkZGRgb2/Pjh07aN++fbGsClEUC+TvbQ2N5ft/7jGtiy/Tp0/Hz8+PsLCwArNfSpo333zT4Grqzs7O5OTkUK1aNd566y2ePHlCz549TeLTVJSog3BWDnme3bt3ExkZybRp0xg/fjx+fpprdyqG6b1ee4O6detSs2ZN5b1bt24dAFeuXMHe3p7Ro0fTsWNHnnvuOdyc7Yxa5VUQBA4ePEhKSgrXrl0jJCSEatWqFbs9RT/U+WcLr+eUnp7O9evXcXR0LDPDd21IwaYSQBRFhg0bxrhx42jevLnR7eXk5DB58mTGjx9vcHm048ePk5GRQZcuXZQRe20viCW5fv06Z8+eZejQoXopw4SEBMqXL2+WYiealt/48ccf6du3L2lpabi4uFCpUqUixyqCgy9XTmffoqn06tWLL774Qhk8NBemvq+GBDnT0tJ4++23+fPPP83ez5LEqGCTIAivCIJwUxCEcEEQPlSzvbYgCIcFQbgiCMJRQRDKbilsA0hMTGThwoVkZ2czbdo0mjVrZpJ27ezsGD9+fLH8iKdOnSIrK6vArCF3Fwf6B1Zla2isVVWWDwgIIDg4WK/C2AANGzYkMTHRLLJoKnY8cuRIUlJSeO+99zh69ChXrlxh2bJlBRL5FcHBKX1ac+zYsRJbCM7U01INCXKWL1+eLVu20LhxY65evWqS81szOhWpIAi2wDKgO9AQeEMQhMJv8LfAelEUmwCfA/NNLWhpxNnZmaysLORyOQEBAVoL2hqaG3rw4EF69epFbKz2CuoJafkBpM2bN7No0SLefvttevXqVWR/a6wsLwgCP/74Iw8fPiQlJUXn/nv37rXI8tJ169Zlx44dDBw4kLy8PKKjo8nOzmbRokUEBwfj7uJA/bwoqnmU5/XXX2fs2LEms9K0PTemzg01NMgpCAL//vsvjRs3Nsn5rRl9LNKWQLgoindEUZQDvwN9Cu3TEDjy9O9/1Gx/JhBFkWPHjvHNN98QHh7O1atXmTx5sl5zxg1VZJMnT2bnzp2EhIQwf/58Tpw4QevWrYmIiODSpUvM//0oCw9F0PqNycTHx5OZmUmPHj00KhpTv3SmnDSwd+9evvzyS637fP755yQkJFjcHxcYGMhXX32FjY0NXl5euLm5sWfPHmbOnEleXh4BAQHMmDGDjIwMg9rVdD21PTeWLnJ98OBB3nrrLYucu6TR6SMVBOE14BVRFEc//f83gVaiKE5S2WcTcFYUxR8EQegP/AV4iaKYWKitscBYgFq1ajW/d++eSTtjSe7evctXX33F+++/T1RUFLdu3aJmzZp069ZNr+OL6886efIkBw4c4JNPPuHBgwfUrFmTDz74gIZNW2Lj145XG3hQ1b2c7oZMjCknDeTm5pKamoq7u7tGq/6jjz5izJgxBheHUVBSfuLMzEyuX79OUlISv//+Oy+++CLDhw/XeZym62mt/m3Iz9/Nzs5W6zcurWjykZpKkVYHlgI+QDAwAGgsimKKpnbLWrApLS2NkydP6pzj/Kxg6hf85MmTfPDBBxw6dEg5P1yVESNGMGHCBFq2bFms9i0x1zwlJYW0tDR27dpFw4YN6dChg8Z9La0wi3P+QYMG8fPPP5ssX9UaMCbY9ABQTbyr+fQ3JaIoPhRFsb8oik2BWU9/Sym+uKWLzMxMDh8+LClRFUw9rGzXrh2//PKLxoj8yJEjjQpqlNRcc1Xc3Nzw9vamcePGhIWFaa0Yb+lhujYXgia3w9SpU8uUEtWGPor0PFBPEAQfQRAcgMHATtUdBEHwEgRB0dZM4EfTimndJCUl8ffff1tajDJP7dq1GTx4sNoAW1BQEFWrVi12kRVLKqqgoCDeeustJkyYwMqVK4vdjr5+6eL4r7V9aNQpWVEUWbt2bbEWMCyN6FSkoijmAJOAv4HrwB+iKP4rCMLngiD0frpbB+CmIAi3gCrAPDPJa5VUr169SIXx4mCp5Sh0YS1yOTg40K1bN7UBs7y8PFatWkX58uUtIJnxVKhQgf/97380adKkyDZ9r79Coc3cfl3rvsXJ0NBWT1Umz2VCUO0CSlYQBPr3718ml15Wh155pKIo7hVF0V8URV9RFOc9/W22KIo7n/79pyiK9Z7uM1oURcOn8JRioqOjefXVV41uxxpTkCIT0nlt9QUWHopg2dFIi8py6tQp6tWrpzYLwsbGhpkzZ/Lzzz+XvGAmwsfHhz/++INVq1YVKBSj73PRP7AqQX4eBIcnma2yfWG2hsayIvguzg52RZRsamoqv//+u9HnKA1Ic+2N4OLFi7zxxht4eXkxe/Zso9uzhJ9OFwsOhBPzOP+7eCLCvEtE68LR0ZG1a9eq3Xbz5k3++usvnJycSvWa6VOmTCE5OZm7d++yZMkSzp07R015NI2yb+l8LtxdHJjft4FZK9sXRtszW69ePV599VX2799v9HmsHWmKaDFYtmwZnp6edOvWjQcPHlC3bl1Li2Q2IhPSmbXzOo/SsvmmfwOt9SbNLktkJNnZ2QUqCCUkJDBp0iQ6duzIw4cPef755wkODjZo2Q1rJDIykpCQEJo3b86dO3dITEzk9ddft7RYeqEa4c96nMRnn33GL7/8YmmxTIKmqH3ZryZgIrKzsxk3bhxLliyhadOm1KxZE0dHxzKtRCF/auSmkUWeG42YM00nKSmJKVOmsHXrVk6ePEnlypXJysoiICCAsWPHAvlz319++WWLyGdKfHx8lDmxt27dUluyr3BfrKVvClcEQKsKjxk2bJjFZCkpJEWqg927d3Pt2jXeffddOnToQFZWFoGBgZYWy2pRfYlMnY/ZvHlzNm7cyJtvvslrr73G4cOHEQSBH374QbmPrrJ/5pTPHGRnZxMZGUlAQECRbYX7Yi19U61YdisshrI08UYTkiLVgiiKNGzYkKpVqyKKIgMGDDBZ29ZiPZga1ZfIlKher3379hW7HXPJZy4++eQTevbsWWB9JQWqfdEUPbcEqiX1WrZsyfHjx0lPT1c7kaKsIAWbtDB06FCcnZ2LVFlKlsn55uBtRv4aSmRCerHatsYIvSkwVz6mqa6XpRPbDcXf35/Q0FA+//zzIjmmqn3RFj0vCRSFrwu/D6IoUrFixSLLWpc1JItUC0OHDlU7k2ZraCw/nb4P5Ee1Vw553uC2zWkZlUVrt7RZkqZi5MiRQP5yy6dPn9a4Xyd/T87fTaaTv2VmEhUufK3AxsaGmjVrcuXKFdq1a2cR2UoCySLVwMOHD2ndujVOTk4Ffk+WycmQ5/DGC9Vo7ePOjK6aK6NrQ9Wa0PQ1Ly5l0dotbZakqVm4cCH9+vXTuP3IrUSCw5M4css89Vh1MaOrH0F+HkXeB1EUWbBggUXKG5YkkiLVwJo1a9i2bVuR37eGxrI8+B7V3Vz48c3AIoV+VSk8I0XTDBXF13zBgXCTyF44t89aZiZJFJ99+/aRnJyscbulc5A1Fb6WyWScP3+eqlXL9khCGtpr4JNPPimwqqYCQ4aY+kZV87/i4cW2bgtTeP0cbdFcTW4ATUtrSFiGP//8Ew8PD43bC99za8HV1dXg2qulEcki1cD8+fN5+PBhkd8NGWIWthI0WQ2Fv+amtiANLTgBpreSJYzjxx9/5MaNG5YWo1h8//33/PXXX5YWw6xIFqkGFAn3xlBcK8HU+YDa5NBkYZvaSpYwjubNm5Oebhofeknz+uuv4+7ubmkxzIpkkaohLy+Pl19+2eS1FA0pPlFS/i5NFrYmn5eEZahYsaLaylCFSZbJWXr0DkuORpaoT1zTKCo9PZ0dO3ZIivRZRCaT0aVLlyKFdo0dcuurIJ/1CLVEUTIyMnjvvfd07qcIhq4IvluiWRuqRoLqe5Kdnf1M1CSVhvZqKFeuHCdOnChSS1HxsGTIc3B2sDN4jrO1BgQkSh5Dc327du1KgwYNdLbRP7AqGfIcZNl5ZMhzSJbJS+SDrOoiKuya8vDw4M8//+S1114zuxyWQrJI1bBr1y4++eSTIr8rLEoRocAQvTTkbWqypqXUKPOj7hrru3SH4u9sGweWLl1aIClfXRvuLg4MbVmTO4/SWR58r8SeSdVRVOGRV+vWrWnWrFmJyGEpJIu0EEnpciIdfen31jjWnYoq8LVXPCzJMjkuDrZFAjXWPOtGUwDLWgpdlGXUXWNtz4zq/oDy7yFDhnDhwgXatGlTpA1RFJUjqK2hsQSHJxHk52GRZ1J15CWTyZg6dSo7d+7UcVTpRlKkhfjop7/Zf9+GNj5unI5M4fzdZGZ09ePIrUSlUlV9UFTzLa3Zp6npxS0NH4HiEBqdwqydN5jXO8CiNVRB/TU2NJMi/9mrRVJSEsuXL6dx48YEBgby84yhdPj5Zz78+muaNGnCxIkTCxxfUs+kJleFs7MzX375JS4uLiUih6WQCjurkJQuZ8SqY1yJlzOijTc3Y9M4HZmiVKrqluodt+kyweFJtPFxo62vp0Xmt4dGpzD1z38RbAQW9m9YQHGUxXn3+vDqsjNEJmbg4+nMnomtjWrLmpRyTk4OO3bsoGHDhly8eJGaNWsSFBREamoqJ06coGfPnhaRS7Gc9YSg2gXiB9HR0QwePJiLFy+WifWbjFmO+Znh279OcCVeTpCfB2Pa1VK+NPWrltcYbVfMMa5ftbxan1dJ+CBn7bxBbJqcmNQsZu0smLRdGvy35mBe7wB8PJ2Z17toHU9tqLtfs3beIDIxo8i1tQR2dnYMGDCABg0aMHToUF566SUEQcDNzY0dO3YQFxdnEbk0xQ9q1qzJl19+aRGZShJpaK9CyqX9DGveg/GvNMDdxYFhLWsofaGarDlFvmWyTI6nq0MRZWsqH6Q2y3Je7wCmbPkXWXZukQT6khy6W5P1G+jtVixLVN39mtc7QGmRWitPnjyhefPmWqeRmhN18YN169bRvn17vv/+e1555RWLyFVSSEP7p4iiSGZmZpHcUWMxlXJRDJ3UuRf02V5c1MmvqU/mkqEksaaPgSGMHTuWVatWIQiC1fRhxYoVDB8+nEqVKllMBlMjDe110KdPH0JDQ4v8bmyJO1Ml1+tK5jfVbKjCQ1t1rgFN7gJLVyAyBaV1MkR4eDgJCQkAbDx3n4WHIth47r7ZzqfLZZWdnc2kSZPKlBLVhqRIn7Jo0SIaNWpU5HdrKd6h6wXXRwHo468trCTVKUdNCtNSSkjKhYWvv/5aqbREhAL/moPCz0nhexAWFsbAgQPNdn5rQ/KRAotXrSOzejOGunnhXuiKlKXiHar+P8UMlMLDv8I+VXVpOtY2Q0vKhYXVq1fj5+eHm5tbAd++uSj8nGw8d5/lwffIkOcwqUNdGjVqxI8//mi281sbz7xFKooiRyIzWH7yodrItrlL3JmTwrKqWpKahuclbVXm5uYiiiKRkZFF1iTSl7LgUjCWRo0aYWtrC5TMPSx8DqUV/DTEMGDAABITLVOt3xI88xZpamoq8yYMYsGBcL3WuylN1k9hWVUtSUsn4p8+fRpvb2+WL19OhQoVEASBR48eFasta7OQLUGnTp24cuUKbdu2tUi+5rCWNcjNyebnn3/irbaf8eOPP1K7du0Sl8NSPPMWaatWrdgRcrfIejcKay40OqVAsKk0WT/qlhz5eMc12n5znLAHj5XKpyQtbLlcTlZWFnPnziUmJoamTZsyYsQIBgwYQKdOnXQeX5pGBCVJo0aN+PTTT0us+HPh++Du4kBGWioZvp3YcSWOOnXqlIkEfH155hVpWFgYw9v7FVGOCmvuw+3XCQ5PYu6+W0DpiuoWlnXjuftsvRxHSkYO07deAzSXPyuMqRboGzNmDHv27GH37t20aNGCgQMHUrVqVUJDQ7l8+bLO45/VCQa6sLGxYe/eveTk5CCXm/8jo+4+eOc8oK8PDHzB2+zntzae6aG9ouhs3759iwwNFUr1QUoGURceUr9qeUuIaFJUo7gOdvl/ayp/VjgYpcheiE4OY8PbzYr1ITlx4gS9evWif//+Rba9+uqrXLlyhRUrVjB+/HiNbVjaJWHN2Nvb8/fff/PVV1+xceNGs55L3X148/V+ODs7m/W81sozbZGmp6ejaVKAwpp7t4MP07r4MqZdyfvgTD2MHdayBtUq5CvAmm5FH3htwagZXf3w8XQmMjGjWNZgTk4OM2bMoGvXrgV+V/QxU7QlLi6OmzdvFjlW9TqU9IggMTGRJUuWlMi5TMG0adOYPXs2KSkpZj2P4j7cS5Tx6rIzhEanMHbsWO7cuWPW81orz7QirVy5ss55wIVf3JL00RVnGKtNPncXB9YOCyTIz4Mv+zQocg5tNSV9vFzZ8HazYvmHo6OjiYuLY8eOHeTaOWlM+O/Rowfz589n7ty5ZGVlGXwdtm/fzltvvcXu3bsZN24c8+bNY+HChRw4cKDAfnl5eVo/opCfzbF582aioqLYvHkzq1atIicnx6B+WwJBEKhduzajRo3i6NGjJmtX03OlWodg/PjxZX7ZZU0800P7v//+m/PnzzN16lS9jynJqH1xhrG65FOkc+k6hynzR69evcpPP/3EH3/8oZxGqpCvk78n5+8m08nfEzs7O3JycoiIiEAmkykXH9R1HfLy8jhw4AB16tRh8ODBtGjRghs3bpCUlISzszNfffUViYmJtGrVik2bNrFw4UKmTp3K/v37CQwMpF27dmRmZtKtWzcqV67MW2+9RZ8+fThy5Aj+/v4cP36c999/n6tXrxIYGGhw/0saJycnFi5caPTijaqoe66SZXJa+biTmycyr3cAsf+eISoqioAA661JYC6e6bn2sbGxXIuI4t+MCnrNJde1TZ/t5sbU50+Wydl47j4iAsNa1jC4zevXr1OlShWePHlCrVq1isinaX7+uXPn2LdvHx999BFr1qzhnXfeQRAEbGwKDqLy8vJ4/Pgx48ePp0ePHrz55ptFZBBFkTFjxtC3b18+++wzpk6dyoABA3BwcGDv3r0cOnSI7Oxsrl27xhtvvEFmZiY9e/akTp06yjYSEhKIjo4mPT2d9u3bG3YRLcS0adNo2LAho0aNMrotdc9V4Xs3ffp05syZQ+XKlY0+n7Wiaa79M22RymQyrsrKs+hwwS+tNqtOl1Vm6TxTVfkUD38nf88ChakNQbGYGoCLg22RtnW1OX78eFavXo2/v38R+UCztVm9enVu3bpFUlISZ86coVOnTrz11lv8/fffALi6unLo0CFiYmLYtm0bO3bsKKJkFQiCwNq1awGK1Ovs0aMHPXr0ACAzMxMnJye1bXh5efHuu+8yfPhwjX21NqZOncrFixdN0pa6577wvbtx4waHDx/mjTfeMMk5SxPPrI9UFEXatm1LpbRwveeS6yJZJudGzGOc7ASuxzwu0VzH+Ph4pZJJS0sD/lPqCw6Ea/QxxsXF8c477xAWFsa9e/eKbO8fWJUJQbUZH1RHbXqYJr/lqZArLD5wjZU//apUourQFDyqWbMmGzdupEqVKqxfvx5/f3/ee+899u/fT69evTh+/Dhr166lSZMmbNy4UaMSNQRNSlTB+++/T8OGDY0+T0lRo0YNrl+/bpRvV5fPXfXeXb9+nfv3zVcoxZp5Zi1SQRCIjIxUWzbPUF+gwjpLTJez59/82Tl7/31EBWd7ZveobzKZNZ4/OZndu3cTFRVFpUqVGDZsGL/++iutK3sxrYsvnfw9aVEnUe2HYdq0adjb2/PXX3/x5MkTvv322wLb3V0cmNShbpHjdPktl++7yMW82riWK0eAj/F9tLGxYejQoQAMHjwYgJYtW2JjY1Niy1jMnj2bGTNmlKoZOxcuXODQoUPFrgdaeISlbSQyffr0Ys9OK+08sz7SkydPcvXqVbU+NUNR+IoUS5IoqOXuxP532xjdvi6+/fZbkpKSlBkImZmZPHnyhK5du/Lnn39St+5/inDjxo2EhoaSk5NDz549cXBw4MUXX0Qul+PgoN+wX59hfVRcEjuvPmJoGx+Dlqy2JLpkvH//PuHh4XTo0KHkhSsmWVlZnD9/Hnd3d7XVzXShr09bwdy5c/Hy8lIGcHNzc9X6tksrUj3SQlSpUoWaNWuapC2FK+Dj7v70f74Krg42VClvz1d9G+g+2EjWr19Phw4dmDNnjvI3JycnvLy8CAkJYe/evfTu3Zv4VBlvzP2VS9nV6Tf4TUaPHk12djYvvvgiAHPmzOHnn3/W65zahvWKD/OV86c4vPRDpUIy5Ywkc6Wg6ZIxJCSEzZs3m/Sc5sbR0ZGKFSsyZcoU4uPj9Spcri1vV9XtFZmQzqhfL/HtwXDlvXjttdcYOHAg//zzD7m5uTRv3pzbt2/z1VdfceTIEbP21ZI8sxZpRkaGyavh64PqqqOKilLGsGDBApo1a8bLL7+sdrtMJuPWrVusPBZBcGr+MhTjg+rwboeC4+3bt28TEhKiHDZrQ53lduPGDe7cucPHH3/M6dOn+emnn+jevbtyGKzJ2iuOpVqSqwGoIooily5dIioqir59+5rsvCWBKIocOXKEFStWMGvWLJo2bapxX32vr2LhR6DAvunp6YwePZoNGzZwLzaRI5EyGjo/xrdmFXx8TODnsSCSRVqI5557jpiYmBI/r65C0QprIDIhXS+r63//+x/JyckaZ7K4uLgQGBjI/DG9aO3jDoBA0Q9IvXr1qF27trLKujYKWymhoaFMmzaNLl26sGXLFh48eMDff/9NRkaGxmMUfZ25/brBlqq5CsfomjUlCAL79u0jODiYpKQkk57b3AiCQOfOnZk/fz5OTk689957fPLJJ2r31ff6zujqRxsfN0a28S6wr6urK7/99hu2trb8cy+TRUciOZ9gy6VLl0zaJ2vimbVInzx5giAIRleoMdSi0mWRKqwBxXRMXVaBKIp8/PHHuLq68tFHHxkl65o1azh48CB//PGHzn4okMlkyiR6bVaOOlT7unTQcyax0EuCHTt2sGLFCvbt20dqaipubm7cvXu3QN6pNqzBXyyTybh48SLNmjUzOlinT951YAUZJ48c4MMPPzTqXJZGk0X6TCrSS5cukZycTOvW/60yWdyH25TDzGSZnC/332bf1XjyyA9WrXijiV4KJjMzk0OHDhm1rrkoijx+/Ji9e/cSEBCgl2KcPn06NWvW5P333zf4fAqLNDg8qch66MZgbkWVk5PDl19+Sd26ddmwYQO//PIL3bp1Y/Pmzdy+fRsXFxeqVatG3bp11c4uspZFAlNSUhg+fDhbtmwp1iwoxXXOkOewPPiezv7Y2tqadLaVJZCG9io8evSI+Pj4Ar8VNxhiymHmhnMP2PNUiQJEJWey56p+65QnJyezfv16rl27VuzzC4LAvXv3OH78ONHR0cyZM0fpRxZFkdzcXOW+p06d4tNPP2Xw4MFMnDixWOdzd3Fgft8GatdDNwZzl9qzs7Nj9uzZDBw4kL1791KlShUOHz5M9erVkcvlREZGMmnSJI4dO8bnn39OQkICDx48UB5vLTVt3dzcmDhxYrFjBYrrLCLo1Z+Zn85jzMLNJKWXvVqyz6Qi7dy5M7179y7wmz4Pt7posSmrEanzXcqy89TsWZRq1arxxx9/YG9vz4ABA3jy5AmHDx9GLperXTtHU+S7SZMmLF++nO7du5Odnc2NGzfYtGkTt27dolmzZly4cIG33nqL06dPk5iYSPPmzbGzsyt2JF1x/Ya1rFHk+he3zZJSVI6Ojsq0Hk9PT8qXL0///v0ZNWoUhw8f5rnnniMtLY0tW7bQuXNnHj16xLp164i5G241NW2PHTtWbCtRcZ2HtaxBJ39PZm6/rrFebbJMTlydrhx8VI4tF6KNEdkqeSYV6csvv1wkcVgfhbjh3AMWHorgg7/+NcuspaEta/Jy/YLLnWRl5xqkTDw9PZkwYQJ37tzh888/x8bGhu3bt3PlyhWuXLmi3E+X1WZvb8+XX36Ju7s7Fy5coHr16hw4cAA/Pz/GjBnD+PHj+f777/VuTxfqrn9x27SW4tvVqlXjm2++Yfz48Rw/fpz09HQyMzM5duwYCxcuBPJrBViSM2fOcPv2bbXbdH3IVK+zIog6aXOYcn/V41cdvsHJyFTa+XmWycLPevlIBUF4BfgBsAXWiqL4VaHttYBfALen+3woiuJebW1a0ke685+zbAnP5cNu9XBzsWfDuQcIiAxtWVPry7f06B3lvHNz+rciE9IZ/9sVopIzeaFWBS5EPWZCUG21M4z0QRRFkpKS6Ny5M8eOHaNixYoF/IgP7tyiYcOGRiVNm8MvaQ1BGXOQlJTEvHnz8PT05NKlS7Rs2ZLs7GydwUJTc+fOHVxdXalcubLaoKs+vlzFPWruXUFZUk+xv+rxMfGJbLqSwvud6zHlZc1Thq2dYgebBEGwBW4BLwP3gfPAG6IoXlPZZzVwSRTFFYIgNAT2iqJYR1u7llSkry7Yw7/JEOTnQYs67sopcK82rsyRmwl80as+PRoXHBYmy+RsOPeAuNQMLkQ95qu+AQR6u5lNRtVppz+fjmZEG288XR2MysOUyWT8/vvvtGzZksaNGxMdHc2ePXs4f/48gwcPRi6X8+qrr5qtTxIFiYmJwcXFhaSkJK5evap0m6jORDMnq1evpk6dOkWKbSvQ59lSGBcTgmoztGVNZZGc3VfjyczOwdnelqEta5Ihy+DgnXQGtaiFh2vp/SgaE2xqCYSLonhHFEU58DvQp9A+IlDh6d8VgYfGCGtORFGkYuQhWtRwZkZXP/oHVmV8UB0mBNXm8I1HZGTn8cmuolXat4bGsiL4LpfuPyYqOYOVx4sW+DAlimHTmHa1mNbFFxd7G7XDXEOGv3l5eezevRtPT09mzJjBoUOHGDx4MF9//TWurq6cPn26RCYpSAvY5VOtWjUqVqyIj48PvXr14v79+7i6llwKWMOGDSlfXvMSOvq4SJTLMKssY7P7ajwrgu/y0+n7ODvY4e7iwOql3yHcOFSqlag29ClaUgNQ9Q7fB1oV2udT4IAgCO8CrkAXk0hnBgRBYNPK77gQdkNZWk4xy6eulwuf7LrJF72KFhpRBC6ae1dg5fF7zOjqVyLyKh7mZJlcmR6kTq5O/p6sOxWl1XooV64cW7duRRRFXn31VVq1aqUMNNy4cQN3d/di59UaYhmXRKnB0ugWeOedd8jMzCyx8506dYr//e9/RrXRs3Fl/n34mJ6NK7Px3H2WB99jZBtvxgfVQUBUPp//+9//8PTUvdx5acVU1Z/eAH4WRXGhIAhtgF8FQWgsimIBT7ogCGOBsQC1alkuf+7JkycMn7McmX834L+XuUfjqkWG9ApUK0KtHOJmMln0eeG17aOQq3DleW0IgkBQUJDy/9PT07G3t6dz587F7ochyrEkFrCzdF3Y4uDg4MCMGTPo3r07np6edOmSb4/oKu9XHBISEvjll1/44IMPjPKNH7mVSHB4Ei3qJCqtUkd7W6Vxohh9xJ7ayif/m2qWvlgD+ijSB4BqmK3m099UGQW8AiCK4mlBEJwAL6BAsqYoiquB1ZDvIy2mzEYhl8txcnLiwKrP2HY5Tme6k7mtGn1eeH320Uc5qetPeno6bdu25dKlS0a9UIYox+IuWWIueSzNsWPH2L17NwEBAfTp04fr168zYcIE5s2bx6VLl6hevTp37tzhpZde4rnnnuPKlStMmDCB1NRUFi5cSOfOnYmLi6NWrVrk5ubSrl07nSOL+/fvExwcbHRVpsLX2cXBVm3d2hb2ntja2hp1LmtGn2CTHfnBps7kK9DzwBBRFP9V2WcfsFkUxZ8FQWgAHAZqiFoat1Swaf78+QBFZuLos5SCOTDWIjWkXdX+BIjRbN++nffee49z584xYMAAk/WprJOXl2eysnCXLl3i4MGD5Obm4uDgQFBQEC1atCAuLo4qVapw7do1atWqRUxMDB4eHvmuqU2bqFKlCt988w3btm3j1KlTeHp60qRJE95//32ee+45fH19qVixInFxcURGRuLn58eSJUsYOHAg9evXZ9myZTRv3pzPPvuMhIQEPDw8sLMraFeZwpBIlsl5//vfWTptGFXdy5niklkUo6aICoLQA/ie/NSmH0VRnCcIwufABVEUdz6N1K8BypEfePqfKIoHNDaIZRRpbm4u6enpZGVlFXHqq1Oapliqo6TQp25kskzOxtORVJVFIkuOx8fHR1lGT0I/cnNzadmyJQsXLjRJXdLFixcTEhLCL7/8YvCxCQkJeHl5FfldFEU+/PBD3nzzTby9vXn48CENGjQgMzMTBwcH0tLSOH78OG5ubjx69IgDBw7Qvn17ZeFsBfoYEppqR6g+j1s2/MzQoUOpVKmSwX20Np75ufZRUVEMGTKE/fv3q7UmtH19rWVutDYKy6ipP9HR0YwdO5Z9+/YZdT5LB3Msdf758+dz8+ZNvvjiC7766isCAwMZM2aMwe2IosiKFSsYOHAgeXl5VKlSBbBcv9avX19kPSp9ZBn5ayhnIpNp7ePOj28GKn9XPI/dq2fy5gvVaNu2rTnFLzGe+bn2NWrUYN26dRqHZNpSPaxlbrQ2CsuoqT/e3t789NNPBrWtLl1J37QrSxVhNhfDhg2jT58+1KxZkxdffJHXXnuN2bNnM2jQIB4+fMiECRN4+PAhW7Zs4fjx42RnZxMREaFMK8vMzGT06NGcOXOGU6dO8fjxY6UStWS//v77b27duqV2W4osW+M9bFDVtcC/ChRphTmiDfbl3E0vsJXxTKzZlJyczMqVK4tVoQhKJjhiLPrKePLkSbZu3aqcoqgP6oJd+gZzFMeev5vM/L4NTGZlWSqY5O3tTWRkJOHh4coi2B988AH29vYIgkDNmjXx9PRk586dzJs3j6VLl3Lq1Cm+/vprRowYwe+//06dOnXw9fVlw4YNVtGvsLAwJk2aVCSTRvXeKQo4qz5jyTI5zva2jA+qw7CWNQoc6+7iANmZHIxxIPCRDS3M3w2L8kwM7RMSEvj9998ZOXJkiZzPUEpyOHfv3j3++OMPpk+frvcxxsinWiqvuK4RS7sRCvPLL78QEhLC4sWL9T4mPT2dq1ev0qpV4RRsyxIWFsaKFStYtmxZkUi/rhiBJpeX6nE/H7zI+FeaUrdG2Vjr/pn3kVpqaRF9KI4PVpdyUd1e0ckOGxsb/vnnH8qVK0eLFi30asNQNLVn7PIq1uijzszMLPU5kbdu3eLx48ckJibSrVs3g49Xvd+AsmaFiMCK4LtM6+JL/bwo1q1bV+rWutKEJkVapof2ihqaiupH1lqdW3U4p69y05Vbqth+7m4yl1dNY9H8z9m7d2+BRHxTJ61rak+RtN2oejwCIrLsPFzsbXQWiVFgjTmho0aN4sUXX2TcuHGWFqXYbN++nT59+vDCC0X0gl7Poao7ad2pKFYE3wVgQlBtpb/eq7wP7du3N1sfrIUyq0jz8vIYOnQoffr0oUePHgUc+tZG4QdSH+WmS7n0D6zK1uNXOB4Ob06eT5s2LxSJnJpaQalOV11yNFJZtOLVxvnXXlFJXUGKLJuPexSdjlsYa/RRf/PNNzx+/Nhi5zfFIopNmjTRuNaXto+sOiXbP7AqMnlukSpqH374IbNmzTJ6ORNrp0wq0oSEBLKzs1m9erVyNkXz5s3Ndj5TDpH1VW7l7AWaOSfi7lIw57V/YFVOHjlAZmYmXhH/0Kh1H8Z1e17tTBdjFJS6PqtOV1VYJwBXH6YxX2Vp6g3n7vM4M5cTEdoXkLM236gq1atX588//2TNmjVFAnclIbei/ieEs3LI88Vqo1OnTixZsgR/f3/c3QtG1rU9h+qUrLuLA+928MnPVT53HxGBYS1r0KpVqyKJ/mWRMtnDffv2ceXmHWp1GFQiyfSmHCLrUm49e/YkLy8Pd3d3oqOjcXd3p3///uTU68jCQxFcDQujp58zmZmZrF6yyGRr5BRWDtr6rLBOMrNzuB6bTnB4EjO3X2d+3wZM6lCX9r4ezNp5g3m9A7SeQ1EEI0OeU+xarOZk4MCBnD17tsjvxjwP+irh/KI54UYVz3FwcEAul7NhwwbeffddvY9Tp2QLr98EYJMrp6OfH25ubsWWsbRQ5hRpfHw8rVu3JsazKQsPRXAqIpHTkSmA6YpX1KtXj5CQECpUyK8cWFI+vGnTpuHr60uHDh2o3/xFvj8eTVunGFo0qMOBY3/T0sGZtlU9ChQfGf3rRU5FptLWpyLfDGhcbEupsHLQ1meFdQIFo/aKc4dEP2Ze74CnVbTslUPTwudQV6JNgTVYq9WqVSMyMpJt27bRr18/5e/GPA/qlLC6vvp4uRbbElVl+vTpnDlzBsh3F3yx7zblHW05Hp5IZo5YQA4F7i75dXHVfVjHB9WhZx0bkpKS6P3c80ybNI7du3cbLae1U6YUaXJyMvPnz6datWrccWkA2FC/anna+nqaVMl9++23Beo4arIiTfWy79ixg+PHjxNxP46mb0xnd1oe245EcSEqFRu/GiT//Tdnzpxh48aNRYZRpyJTlf8aYykVVg76ugUUC9wproNCBsVy09m5t5T3p3BJwJ6NKxcpgqHAWqo7tWvXjsjIyAK/GeMyUaeEzdlXOzs7/vrrL2rWrMmCU6mciUxWbnOys9H43qjK1D+wKhnyHEa8UJm2XllU8fPl0aMK1K9T85lQolDGFOmTJ0+oXbs2zZo149rGP2hU3o8x7dqZ3GLp06dwXWv16PsChEanKIe6gd5uBRTw40cxXL58mXv37tFrykJle81rVSTIz+NpsEGzZdLWp6LSItVmKelS+sYGfGTyXDaeu68MPCnqutat5FrgGulbEtBaIvl5eXkMGjTIZO2pu87m7KtcLqdTp05kZmYyo6sf8tzbJDzJIvyRjB6N8ufGq6tzq3DfZMhzWHvyHj+dvo+PSxbO0Q+ZMWUS/v6ldzmR4lBmFGlSUn7gYuzYsQBkZ2eTlJRk0SCFvi+AYq2bWTtvsGdia6UClmdl4XAnmJUrV/Lw4UOSZXKS0+Vcj03j4+7+BaK1mqK4hYfzxpTqA/2tbNX9FKsLwH+BJ3cXB1YOyf9oKJZQMeS6mTOSn5eXR2RkpLJ8XaNGjQDYv38/Pj4+yOVydu7cyZgxYxg9ejRHjx5VWzwETDMqMWdf165dS79+/ahWrRoAP74ZWOTeqXsu3F0ccHGwZeGhCFr75AeqImWOpPs1MYuc1k6ZmWv/559/surnDco5wW3atMHGxoa///5buU9WVhZt27YlIyOD7777Tm2gQJXIhHTGbbpMaHSKUUsN63qB5vUOwMfTWRl8qZ37kPeCvPnfwCBq1qxJVFSU8uEe1a4W695sWiTlRRHFXXAgvMDv+s7d1reewLqT+dbiupNRQMG59Kp/K847c/t1Ovl7Mj6oDq193JW+Um3XyNKrgCqWm96yZQuff/45MpmMr7/+mgkTJlC1alWys7OpWrUqXl5ehISEaFSiYNjc+eLWJTD0uIcPH7J27Vr27t3LyZMncXBwKNAOoLz+2p6LTv6eBPl58F6H/KV6+vs7cfvAejIzM/nzzz8N6kNpp0xYpBkZGQwfPpw1J+4W+Ho+ePAAf39/jh07xuTJkwkNDeX999/H2dkZR0dHHB0dWb9+PQEBAbRs2bJIuwrlFJ2cQWRihrJdUxPo7caGt5uxNTSW9Lh7DBnQm40bN7Li+2+UM062ntM+3NUUxdXXKtbX6rnyILXAv6oWC1DAb6aYo92iTqIyNUZ1JowpciHVYawVOHPmTGrWrEmFChUYPnw4ubm53L9/n1OnTlGxYkWaNWtGs2bNAHRmRRgyLC+uL1Tf4+RyObNnz6ZLly5kZGTQo0cPevToody+4dwDVgTfRSbPVQYLtT0X/1XHd3+aVVEXBrchLi6O06dP06NHjzKfP6qg1CvSnJwcmjdvzv79+4s8tKNHjwYgNDSUyZMnI4qi0p81YcIEIH8lxcDAQLVtK5TTuBdrExL92CAfVWG/py4UL0Pa8fX88ssvvPzyywW263ohNUVxTT0sbFKjIheiHtOkRkWNcikUmGqQSR2aciGNVYTGBmf27dvH5MmTlf9va2tr0Lx6VQy5/sX1hep73JIlS/D29qZLly7KZUwUJMvkXIrO/zgKaJ9KrboEc5CfB538C67FVKlSJRYsWPBM5I8qKPVz7UVRJD4+nhxbx2IXYU5ISOCjjz5i9erVRsuj4JUlp4lKzqSWuxP7322jc/+DwaeZ/dNeOtZx4ss5s0wmhy4MVVqFrUpDji08Z16TRarYL8jPo1gVo4xVxG3atOGff/4p9XPpVUlMTOTu3bv4+Pjg4eFRZLvimrf2caepd0WGtayhcwnmNj5unI5MKVID4ccffyQhIYFPP/3UXN2xGGWyHunp06f53//+R/ny5ZVWyIID4QbXc4yLi8PX15eMjAyTydbe16PAv9r46aef+P2XtUzv1dQkStQQn9mGcw9YeCiCDecKL8OlHlULa+b26xqvtToZVP1tyTI5e67G0ah6Bdxc7Asc28nfEx9P5yL+VHWoO4+xPtYTJ06UKSUKEB4ezpUrV9Qq0WSZnAx5DuOD6tDMuwIrgu9qve6KvN7s3Lx836iKJXzjxg0Ao1cnLW2UakUaEBBA3759ldHsNj5ujHuxtsFFmBs1asSgQYNo0KABubm5JpFtYgcfpnXxZeJTX5MqhV/+rVu30rVrV/r376+zXX2UpCEBDsUwTt1wTtu5tobGEhyeRJCfh9Y8T02BJcWspRXBd9l47n6B8xy5lUhkYobGtovbV31IS0uja9euVlsprLhUrVpVY22AraGxLA++h4tDfl0EdcN1+O956Nm4Mm183LgQld+eu4uDcluaPI/KlSs/M75RBaXWibFt2zbq169P06ZNWXcqih9PRwPQ1vdxsfxiderU4erVq6xYsYLRo0cbbZHok2qUl5vLvb/XcfToUd5++20++ugjOnToQNeuXTW2a6oVRRUMbVkTZwc7tftqm6Kp2L+5dwVmbr+uDHLN2nmdR2nZfNKjnsYPWmRCOjsu5yu+F2pVQETQOGtKl1Vp6hxLe3t7hg0bhiiKOlfiLClMkUJVq1YtBEFAJpMVUXKqEyEUfusWdRIZVSgAqHj2MuQ5RWadKSdapIYy/63OhISEmLW+hbVRahXpw4cPcfCsyVenLjPuxdpMCKqNLDuPDHkOyTJ5gQcuWSZn7cl7XI9N55Pu9YpEiBUPavf6bpw9e5Z27drRtGlTs8mueHC3L/wfJ/85QNu2bTkVcoXbedVpmat9kGDqHEt10/0UaJuiqTjHuE2XlQEjgND7aQDM3Xebul4u1KvkwqbzDwr4QRccCOdBahYALep4MKxlDVwcbJUzmvoHVjVIflMF0+RyOd27d9e4rpelMMXMJkEQqFGjBm+99RZbtmwpsE212IymUYbq8F8EzkQmE+TnoayMr9j/yNrfCQurzIMHD6hUqVKRqvtllVKpSG/evMnLL7/M3OBE5RIIK4c8r3SYOzvYFXjgtobG8tPp+0D+S1w4uv3fg+rLhAkTePfddzlx4oRJZFUXUFE8uPVyJmCbm8nGjRvZdDGevf/cZc7Puwnwqck///zDO++8Q2pmTgElV1hxmMJa0fSiKhScOqUdfDuBmTuuM7mjDzJ5DhnyXEa1q0V8WhaRCTKc7G0IDk/iUnQqaVm5yOQ3WT+iGckyOX6VXJHJc2hc47+gRv/Aqso5+YXlKCkEQaBHjx7Y29vr3rkEMZXVPWDAAHr27Mlnn31GaGgov/32W4GRl2opvMIohv+KUYbiuSj8zC1dugSAhq+O4mHi96xessgomUsLpVKR7ty5Ex8fH2Z0fQnV3ElND1z/wKokpWdxPTZdbbWcTv6enL+bnO8XSsvF29ubq1ev0rhxY6Nl/S8XNYwNbzcr8OAFBQUpCy2/1rwGeXl59Jowm5nT3qNLly707t2bOq+M5khSfqqRtgLOmrbrg6brps3a+9+2azzOzGXh4TsE1qxIcHgSj9LDufs03zZDngfkTw0FiEnNVCr9H09HF4n0bjj3gODwJNr4uBmkMAzNRT1+/DjNmjVTLsf98ccfIwgCvXr1YuXKlSxfvtyqrFEwrdXt6OjI7NmzCQkJ4cyZM7Rr10754VCdrVTYGFEd/qt+uBX3VCbPLVA60b3jSMr7Wtd1NCelsqdTp07llVdeUeZOFrb0Cn8l3V0cmP5yPX58M1Dty6ZILD5yKxEfHx/atm3L/fv3TSLrjK5+ygIdhQMiqsEcdxcH3nnJl+peFVmxYgVvvvkmkydPZviL/ngnXtCoXPSdkaSN4kS5K5d3VP47o6sfQX4e3Ev6L+vhkx71qOBkS64ItgI8SM1SvoDq5FVYQU29K+qceqoamNI0o0sdkZGRHD58mFmzZrFnzx4CAgJo27Ytw4cPJzs7m6ZNm5qs7KA1IwgCL7zwAikpKQXqRqgO3zU9T7uvxhcI7ik+5AIiI9p4cyoikebeFXi5Wjanz4cQHhVTIn2yNKXSIt2zZw+HDh3inQ8+Ye6+W9SvWp4x7Yqf7qJqkYWEhLB9+3aj1n1XHW77eLkqZy0Vfjg1WZOKYECXLl24e/cuA59z12u5B00ymHKqpaJdR1uF31Rk0uYw5vUOoEmN8iw9ll+LcuaO61Qu78jjTBkNqrrSrVFVrfP9FUEvVT+pOrkLXzNddTllMhn79u2jefPm9OvXj9OnT+Pk5ER2djbBwcFUrpy/KFu9evVo166dya6PJcv76UvPnj0LTG9VHb4XjjEo3C6qy4hAwXdn5vbrnI5Mwd72Hi0aBXAwxp4B07+l7/NVibarwVfv9KVyxbIZzS+VijQoKIgaNWrw+a5/ORudzunIFDxdiz/8UX25mzVrxttvv41cLlfOQTaUwi+7JuWhy/eVnp7O4sWLWbTIcD+TuUqvKdptXK0cAOGP8q3QIT9dIsg3v3iFnY1AsiyH3DyRNj5uRQqsqEM14KFN7sLXTNOMLrlczqBBg3j//fc5ffo0/fr149ixYzg7OwP5RY0VStSUWEt5P32ws7Njz549vPDCCzg5OWl8HlVT3RTLiChGBqqBQdWPmiI3uGfDlvx2JpIj5x4xcu4aevk58c4775RoP0uCUqlI3dzc2LBhAz1e7IqNnRt1PJ2RyXOLROuLw4EDB5g9ezbDhg0rdhsFfK5a0OX7ysnJITa2ePmR5iq9pqg9+delh0W2BUfk17LMyROxsxF4nJmLTJ7DtL/+5UFKJi8HeDHtZT+t90iX3Iprpvoiq2vPxsaGcuXK4erqyvXr14mMjMTX17c4XTYIaynvpy8JCQnk5OQA6p/HZJkcmTyXkW28cbS3Vf6u7oOh+KgVtsrfCvKnfIUKNHOriW9N6107zRhKpY9UFEXeffddXusWxLo3m1LdzUXnbAx9CAkJ4dtvv+XatWtGtaPqc9WFtqT3+fPn88MPPxRLBnNWULr6MI24tGy127xc7HihVkVy8vJ9npcfPOFGXDppWblsvRzHzO3XtU4m0FduXYn4dnZ2/Prrr7Ro0YKJEyfi4OCg8VoXt+qSuuMtXbnKEB4/fkzPnj0pV66cxn0UJRDDH6UXeMfU+boV12HjufsF7o3imnw68wPOnz9v3k5ZiFJpkaampvLGG2+we/duXF1dTWYF+Pj40KdPH6OT8fsHViUxXc6piMT86Y4a8lYL13t0uXeSZs2aERcXR/Xq1cnLy1Pm41kLimGeJhJkOaTeT8UGyHv6WyVXezJz8qjl7qSc9mnssFffe75mzRqaNGmCt7e3RrdBYevKUD9naRrOA1y+fJmLFy9y9OhRevbsqXG/yIR0TkUkMqKNNwObVqNFnUTl9Va1XgtH7scH1VEbUFy1ahU+PkVn+pUFSqUiXblyJYsWLVKmsJgqPcTZ2ZlNmzYZnZDv7uLAnUf5vlvteav/KYLYU1sJu3CGAwcOkJGRwZkzZ1i0aJHGylSWon9gVZYfiyQjO0/jPopNTnY2NKpens971sfHy7WAgjI2KKPvPc/MzFROjdSWHqf6r6GL7pWW4fyePXvw8vJi4sSJHD9+nKFDh2qNAyw4EP40eGSDj5drkZlOoD0QpXp/Y2NjGTx4MOfOnTNb/yxJqRvay+VyKjdoyYfB6YRGpxQZlkUmpDPq10t8fTBc76Gaoo2HiY8ZMWIEw4cPN1pORUqQumiyYljUwjOHB3dusfzdfpw4/DcdOnTIL2Dy++/cunVLr7n3piI0OoVXl50hNDpF576vNKyEq4OArZYZlPa20Km+JyFRqey+Gq9xFVJj3DHJMjlLj95hydFItfd6586djBw5UlmSUFX5qj4zhYfj6mZ0KYp8RyakFzlPaRjOP3r0iN9++41WrVpx9uxZnJ2ddQZTZ3T1o42PG36VXDW6Q1TzfwGN97dixYrs2bPHLH2zBkqVIhVFMX+IdlVOdGo2s3beKHLDFF/Rn09H6/2SKtoYM/9H9u3bx4oVK4oto+IBc3OxL5DjqopiJs+CLcF0fKUXLi4u7Nu3j3Hjxin3KW7GgKFyKl4Q1eVOtLE1NJZtl+MYF1SXneNbFpk82tbHjSA/D7JzIT4tv+3M7Jwi98kU+a+KdB1N/vGjR4+yePFi0tPTixynSYmHRqewJyyWwS9UV05/BPX5qsb6VkuSzMxM3n77bSC/vqo++Hi50tbXkx9PRxcpLKOaPzqtiy9NvSuyPPiexvu7dOlS1qxZY4aeWQelbmh/48YNrsXJ+HjXTeb1DqC2Z35emuKGjXuxNncTM2jnq/8MGcV+NpGPuHsrk08//ZRvvvmmWMN7ff1lW0NjuZRXG7t67Rn1VmeN+5mLwnLO6x2gdq35wjOHVIuVvLPpcpHJhFm5IpM61AagppsTF6JScba3LZLJoM/QPFkmZ+O5+yTL5EQmZhapk6DIIBAR1N7r9957j1q1ajFnzhwqVKjAX3/9xcmTJ7UOxWftvEFUciZEJFHDzVm5T91KrmTn5hUYYZQm3+j69euZOXOm3vsrRhCK+yWT5xboq+J+vtq4Cm4u9mw8d79AIn/h+ztq1Cg6derE66+/jp+f+pzf0kypUqS//PIL586d49tvv2XPxNbK31VvWEj0Y6KSMxjoVl3voZbipme3qIZc/ip3797l6NGjLFq0iF9//bXI/tr8e9peUtXjFNszqnUmLCzMoHOYgsJyBnq7FbimkK9Eh/18kWRZDooq9imybI7eSmDNibs8zixacjAkKpUlRyM5HZlSIOigCFKpqyqkqa8Ki1PBpM0Fp9m6uzho9WHOnz+fdu3aERAQgL29PRs2bEAQBK2FWhQflFY+7gWWUPn56bTWwopc9V9rJiwsjLNnz9KmTRsiE9L5dPdNREQ+6xmgdtSkLgCnWndBkZniaHeHQzcSEIE3XtD8znl4eLBt2zb279+Pl5cXvXv3LlOl9kqVIm3bti09evTgys07nE/8r/Sb6gthzMNtb2+Pvb09jRo1okqVKtSsWZNRo0bRvn175bBIcT5NloghK3WOaluLpIDezJx5mh07dhSYrmeotWOo4tXHIlxwIJxkWQ7uLnZKS2zBgXBCovKXpKjgZEtdTxea1XJjQNNqfLHvNmcik6lftbxyrXqFLNpya1XLsylK+inupaKa18mIZOU0W9XIurYVET7//HMePnyoHFm8/PLL9Bv8Jo4NOpAhz2F58D3O300uUIW/tqcL3RtVJiM7lxFtvMmQ5yiXkNZUi0A1p1XRH0M/gOb+cI4dO5Y2bfJXalhwIJzzT++humAoFP1IFH5eFPdm5fF7ylHJyQjN2RyQnxWTkJCAvb09L7/8MsHBwfz000/Af8sClVZKlSJVrJXdYdxcZP7dlL/rM4vIUFS/mu7u7uTl5bFkyRKio6P5+Iv57Ny5k16NWgH6vwTqlLyHhwd3795l8+bNBRSpoR8EcwwzVWeqKKyWGV39SHzyL/dTMlnQryFB9f6bYrhwQEM2nHuAgFjkWvy3UFpRi1TRx8LDR1WLU90SJwsPRSgX2FPX7ypVqlClSr4SfOONN/j88885nVKOpYciGB9UhyA/jyLpWKpWsLuLHcmynCIFPAqjeu2BYt0Hc7oJ0tLS2L17Ny+++CIjRoxg+hffkvQkk0dpWYx7sXaBfVWvszY53F0ccHawI08EAfBwteOrvg10ytKiRQsAli1bRlZWFu3btwewqvqvxaFUKVIF//w4nz8uRBdQMuYYXgmCQMeOHXnttdeYOXMmvXv3Jj4+nopOdlR8eI5y9iMB/V8CTUr+22+/VS7RoGtfTRTHEtf1AVA3/dLHyxXPco78G5vOpvMPCihS1epByTI5ZyJTlIv/KSyYxHQ53x4Mx9HeVllCT9Wy01S2T51FBPmWrmp+Y2Hkcjm//vorqampODs78/bz9alYseLT/WsUUM6RCemcjEhiyAvVORGRRFRyJj6ezjqvqbprb+jzaE43gSAI2NjYsH79eoYPH07U1fPk3L1PnFMDQqIfF1icUfUDpWu9LFWZDbWiK9Wso7TiZ8+YxpAhQ+jcueRjBaai1C1+J4oiK1as4K233ir2F8xUw6j9+/fz3HPP4eJeyaj21q5dS25urkFzkE3Rh8KL0emLttJ1im0RCTIepOQrIoXvVTUhHjD4vMVh1apVhIWFsXTp0gK/q7t+r685z9WYJwBMeqk2Vx6kFXupaGsvXpIskzN16Z94Pb7NRx/NLPBBUeSFmvP+LDkayYrgu0x8yYc+vnb4+flZXflCdZSZxe9yc3O5cuWKUcMAU63zs2nTJm7fvs39iJs04H6xX5iqVavy4IF+i88p0NUHTXmPqr8rUlQUFZf0TeMpXL5QFcUQ/iU/D3w8nZnXO0DpQ+zk78n4oDqMbOOtjPCqphAVTicyNr3o/PnzlCtXTu1SyuquX1Tyf2UAVx6P0thHfSjOM1aS6VSO5PByTXjvnZEFZFUso21sapouFGUTT5w4jr+/f6lQotoodUN7Ozs7li5dilxe/IdNdViorfCFLtasWUNwcDARERHs2rWLdu3asWXLFl577TW92xBFkd9++41mzZoZdG5dQ0FNa8YX/n1U21pK60Amz+VdNYv1GSqXIiVJEWFXtUQLt696boVbQPG3arFgdcU0Np67j4igdungffv2cflGBBtPRdC2W98iASmFnKrFbr7u15Apf4Qhz4NPuhuXolOcobo2F5EpLFzVNn5duxZXj8ociBaVAUBNgSVzWNdDW9ZEyM2mX2DZWNep1ClSgEWLFuHm5sawYcOMWpdd8eAqXlxDH5SQkBDWr1/Pr7/+St++fcnKymLBggV07twZd3d3jedVnCMiIoK1v/5GvEcTRo5/16BroMuHqqlOp7rfta0kaiiKIMTCQxH8+/Ax8/s20KpUVM+tTAmT57DwUESRKYeqqAaFXBxsi1yLY8eOcSzentzneqt8PP5TUKpyKo4PqudFyKyORl8DRfua7o+mZ1bbdTJFMErRRlJ6FqdyAwgsV4XNOtpUnQKqab/iKFp3Fwcmdq7P6dOnSYm7zwsvFBktlypKpSIdPHgwdnb5ohv6gKmb5654cfVtQ0Hbtm1p27at8v8dHBwIDg4mLCyMZcuW8csvv6g976i2tTh+/DhLly4lNKsSts0GsDU0lls7ljFz5kyaN2/O4cOHGTBgAEuWLCExMfH/7Z1neBRV24DvSW+kEloIEEKvASKdUAQEQcCoKIIvKKKIvjaQZkNQKYpiAV5RQD5pCkTBghQBEzoBghCpIYQEEkjZEFI3yc73YzPLZjPbshtKmPu6cmV39sycc6Y885xznkJQUJBVWquxOJ2G26Vh5EsRjRit58ljC5FhdXSr6WNWHuPrJ9vKhr5T5asREZgU0VAX51Iqp28GJbXT0MzNmDH+qlWr+OWXX4g+dIxf4zOMLkjJCS5TQsFempmxe9aU8K3sYpSc7fK+hCzO3nTmeuxpXu0fbvKY+rFIjZWzRchnZmbaLQX6neSeFKTBwcFER0cTHh5ucexPCcOVRsMH11bc3d1p1KgR/fr1o7S0lMOHD9OtW7cKD0Jqaio7og/w3g87cXJyIlS4xuHsbEqd3Jnw2U94B9Zl1apVtGnThnfffZfw8HCrh/+WYCwqurUYCpm5I1oyZuUxEjMLdLaKqw9fYWn0JTLz1AR4upCZp+b7A8m8FNHIqMDUb6cp0yh9AV1QUMCYMWOo5eOhe7Alk6u45GydB1dYsG+FB9+YTaslmpmlVEYoVtasT852WUq73Cg7kXFd6+PmZtvKvLn+mHoBDR06lC+//BJ/f/97Wiu9Z2d4P/74Y3JycqyK/QnyASbsHXSiVq1ajB07ll27dvHWW2/J1jF//nz6T3iHZfuv4O7iRJ9uD9CoUSN+jkvl/07k0OfzA1xGa1o0Z84c6tatW+nYpGB8IUNacLL1JbL68BUW7kxg1PKjJGbk4efhwtdPti0XuEUaxp9Nu8nCnQmcuqKNylRUXKpr44xfTssu0phqp+HCzooVK9i1a5dsO83FFJDqEREq5CaSNDNrF+cMuZ1BTuTOW0hNT97vW4sA51KzISMtaau5MuYW3sLCwggMDOTkyZNcu3bNgl7dfdyTGqkgCGzdupXCwkIiw7SpI+5GN70zZ87onAj0mTdvHm5ubnwzfVw5s5O1a9ey6pFInBwESjQi7/56lofbaH/z8vKqYGtqDZUZTlqDJCQvqwqZs/U8PUK1Q0H9aQT9vEy7zmWSlVdE7OUbusjrpoaR1gx7fX19K5g7SRjGFDDUlozZtOr/N6a1VpaqNJUydt4cHBz48ccfmTBhQpW3x5TGqspXc96pEcGCB/O37ObBEA+efXqkzXXebu5ZjfTw4cMsWrTojoQwszTkXEFBAZ999hkFBQXltvfo0YOLFy+Sm3mtXNtnzpyJY2kh80a0wN3ZgTmPNAe0N9vWxGL6DR5W6TYb0+jsZXIzunN9nu1Wn64hfrSs4ymrgUjXKqSmJ+O7N6BLIz/8PJwIremuM4+a3D+0giG4uTbq3wPZ2dn4+Pjg7+8vW1aKKSAZoRvTlgzvK/3vxrTWymIvczxrCAwMZMyYMXz//fdoNOVjy9q7PaaeUamu+dsvsPdmIPm12/P888/rYsjeK9yTGilo50kbtunMxLUnKm00XVn0h4eGgT4kNBoNv/32G/Xq1WPOnDmcP3+eoUOH8tJLLzF48GDWrFnDsGHDOH78uG6fq1evIooiTz/dXqeJgvZmW340C9+kgzwxbHCl2qyvmRiL0F8ZzVT/WG8NaKrb5u/pqhPaxjQcyZf/oz/PlwVGkW+DJW0URZFx48ZRUlJCfn6+yfQZ+tgyX2nKE8sYcs4MlrTBcG7XVhwcHOjatSuLFy9mzJgx5ew4q8LLypylgrQg+EibQGpERODo6MjSpUsJCAhg5Mi7X0O95zyb9BmzbB97L2YT0cRfdoVaDmMX1JrhjP5N3TDAw+h+kv9wTk4O3t7ebN++nZ49e+Lh4cGJEydYs2YNCxYsAODff/+lTp06spqU1LYR7Wrj5+HMjcISm4Ze+h5NkjC1x7GMCTljZaTzOG1gE86n55db2NFvk7lrk5WVxfPPP8/y5ctRqVQ0btxYdp+qGkKb8vQy5D/fHyX2cg7hDbz5v3HGbSgN2zpk8UESMwvKeYrZg3fffZfevXvTv39/ux1Tjsp40R09dYY9SUU807MpDWr73xVG+zZ5NgmCMEgQhLOCIFwQBGG6zO+fC4IQV/Z3ThCEbDu02Sw39qygtZ9oNKe5HMaGLdYMZ/SHh8b2U+WrWXEgGVW+Gm9vbwAGDhzI2rVrGTRoEI6OjjoTLtD622/atEm3r1wE9wn/GcVTTz2lq3P14Ssmh7yWLDDZOjViyWKVsTINAzyI7FCPtkHe5dpgeE6NtbGkpIR58+ZRUFBA+/bt8fPzo3HjxrLHMLbNHlgX9Fkw+C+PYVs/GtZC5ylmL1QqFSqVSpeypyoxvAcsmVKKy/Fg+dEsXv70B6Kioqq8jbZgViMVBMEROAcMAFKAI8AoURRlU20KgvBfoIMois+ZOq49NFKNRkNhYaFV+9hDI7XkeMbewCUlJaSnp7Nx40bc3NyIiIjgp59+4uWXX8bX1xcHBwej+4qiSGlpKT0fHMSTMxZx5FgccSVBRt/y0nFeimhUKYcDa/pbGYz109I64uPj+eCDD1i/fn0FLV0uzB5ULsSdIYbtk9NIv95zkSXRSXQN8WPhY6109VmqvVpyDmy5Z5fHJHAx4SJH1nzC/j07cHZ2tuIM2I507SOa+BsNjiL1r29DNxoH1eLEiRN069aNq1evcuPGDVq2bElcXBwNGzas4ABTVdiikXYGLoiieFEURTWwHhhuovwoYF3lmmkdgiAwY8YMqwx65bQbuQfDWH4eS44HxrUwJycn6taty5UrV8jLy2PBggVs2bKFvXv36oYuhvtK7Zn24WfMmTOH6W+8wsTeoXTxL2RcmA+NSSMjI6NC26TjCIjltBtbFpgkTclcWmVLMHaOLNGSRVFkypQpfPHFF9woLDFqNvX7qWvl/Mit1b7lzpWhtigXe0DK93QwUVWuTfplTV0HS9paWQ076ngqKw6lsSfDg2c//Pa2C1HQXnv9MIZy6M6Bny/f/J3A9Pc/pLCwkEOHDrF582YKCwtZvny5TdYs9sKSxaYgIFnvewrQRa6gIAgNgRBA1ohPEIQXgBcAGjSwg8mNIBAaGkppaanRPDSSkOwU7M3/YpJkNQHDxQxjfurWYM6saN68eQDk5uZWWBgx3Fdqj1uWNyP88xgxYgQAr03UBsMdOHAgLcPCaTdiYjntxJjDgS0LTPpeS1FxFdMqW6Ml2eLT/eOPP1K7dm08PT3ZIGM2JfXRWGpgS5E7V/oLJMZiNYzpHISAaDQNirFjW4JhGhBr+za4hR+z5qznzTffZFSXhuZ3qAIkpw198z9jRMWl8cXfl5k8dSEajYaHHnqIhx56CI1Gw/z58/n6669JTU29rckiDbH3qv1TwEZRFGVVRFEUlwHLQDu0t0eFkyZNIj4+nqZNm8r+Lt2sIQHuJGYWICccDVcpjfmpVwVeXl5mBYjUnqkDRskOB19++WWir7uwcGcCKSkpvD+ye7nfDQWWLauy+g+AnCCxNH6BXJ/lBItcudzcXPbu3Uu3bt3w9vYmMsxN1x+pjCUeOaYwJayk86kfjEUuU4J+GhS5flT2OthqabFp7SqebFODcT1DqmTxzdIXoqU2zObO0/Dhwy1O6FdVWCJIrwDBet/rl22T4yngZVsbZQ3Xr1/n9ddf5/fff5edF5NOvr5GaojhBTXmp24KW+YOzT0Yptqjylez7ng64wd1prgkiYs7N/LGgQ106tQJR0dHDh48yPTp03HzCSjXPnsY4W88fpWVB1LK5X/XLSbkqVkafYl9CVnl5ghN9VnugZErd/nyZdatW8dnn30GyD+QtvbREmFljWuk3PGMmaSZu3+sEcDR5zOYsfk0c4e3JKJpTVJTU2nXrh2PPfaYzQLZGNJxpeDQ0rbKCmxz1zI4OJi1a9dSq1Yt3cLu7caSOdIjQFNBEEIEQXBBKyy3GBYSBKEF4AccsG8TTVOvXj22bdsGlDfuNZwXCwv2rXR8SUvmFG1ZEZZb0TSVr92w3n8cQrms8WXGiHAEdR5hYWEMHToUNzc3goKCCAwM1Llwrj5sXdxTY3Uu3JnA6TTtHLJ+/nfpfEveSoZzhMb6rL+voZ2hYblWrVqhVquJiYkB5K9P9PkMenwaQ/T5inPHxvbR32bKGkEqB7f8/qVtiRl5umu35nCK7p4wZ91g6f1jjcBV5at5Y2M8qvwSZmw+jSiK9HxwECeLa+PuW9Mii4vKYDj/ae2zUZk5/LNnz95R8yizGqkoiiWCILwCbAMcgRWiKMYLgjAbiBVFURKqTwHrxdtsmHr16lW+/vpr3nnnnQrGvfa6QWzVTszd/IZv3Ki4WyHipFB0xh6aIDJp6JxLv2YBZF27xAMPPMDYsWMB6Nq1K8uWLWPCK6/rvLAERJtX3vXPs/6KuD7m5ggt1RglTyLD9m7atIkXXniBhIQE2eszY/NpnQDZN6VXhePK7WO4zVj7TO2rn0NKmp/t1yzA7PnWv39MreyvOZzCkuikcqMAY0TFpVFQrMHd2YG5w1uyZs0aeo19iyX7U3H38DDZR1swNv9pyfNobXAY6V5+fdrbxMfH06BBA+rWrWtbByqBRXOkoij+AfxhsO09g++z7NcsyyksLKRTp06cO3cOjUbD+O5aOzvDBGtyWCpQ9G9yY/uYEgzWDqEiw7Qh4o4n3yD6QhYzfjltVJj+kyGSVOzF+r2nSd6+gsWLF+t+u3z5MsuXLycqLo0DiVrHhdGd69s8pNPvq7HzbDhHaAty7Y2JiWHq1KmA/Ets7vCWuiGtHHL7WDpkNrVvv2YBtKl3rVzAacO5VDkvJf1zekuQVJzPl7T/Y8k5uoDU5topvfAOHjzKzClTiLlaanct1BDD58HS+yxKZuHQXHnp3F6J2YGmUWcmDnQkpF7gbU2md8+6iEo0btyYxo0b8/333+Pu7k6LFpYbLFsqUPw8XOjXLIAZv5wmNNCDlQdSzO6jj7WLCpIQ0n87G66QZ2RkEBkZyTtz5jG5fygNSq5QVKtWuZvnzJkz/PXXX7z0+hRd/ZKGJ9ceS18s9rQl1T8WyM+lGWtvVpZWa5F7iUU0rVlBEzVst6HFgJQB1Rzm5mQNXyCG7TflYqzKVxMa6IG6VGRir4YVFvOGtqnFn/HXOJioYs3hFJNBU/w8XOgU7M2Y74+hyi+hb/MI6tfyY3yjuyefvFycWbB8kVC/fBSPs3BnArv++owf3h5Ly5bms5rai3tekEpIftbWeGpYI+AkEyR1qWg0+Icx4SLdINKDKgUxNofcEEkURZKSkggKCuLLL78kLCysrG54bepM3b7Hjh0jNDSUFi1aVHjwjWnP+i8WueE0wB+n0ngr6jQiEHtJRXgjP5sEqn6dIJ/K2M/DhafaBzB4UH/GjBnDzZs32bx5M46OjsyYMUNXzpyAN/XijIpL06U1MZd+2VoMF5W6hPhRqhFlvZSi4tJYeSCFiCb+7E3I0k3xSPvvOpdJYmYBEU38dUFT5PojMf0X7RSHK8VMG9kbD4+7S4gaDuPl7k1VvvG0MvrlpWekf+N2BNe6PQb6EtVGkIJ2uLdo0SLWrTPvD2CtViWX410fc9qtpQ+qsbBuEikpKURGRrJu3TrCwsKM1r1jxw4yMjL45JNPzPZNotzb3Uh/3tlyVqez/X0hi78vZHElu4D/9qmcKY2pYbI+q1evpm7duvz5558MfCQSx9YPsXTqWN3vlsytmXpxRobVIV9dWi7lSVUQFZfG+tirhAS44+Ne0RA+MuyWnW7ret4VXtqGfTAWNEW6j8Ib+HBZVYi36jwZV2rSoLZ8VKw7gaXDeEvXDKRnJU2VyyNvfcH/po2leaP6lJSUUFJSgpubGwUFBTg7O5dzz7YHdz4KgB3p3bs3a9eutaistSuJpjJngnmf88iwOrwU0YhJEQ3N3jSm2lWvXj1eeeUVmjdvbrLuadOm0aZNG5KSkizpHiAfKs6wrQ82r5iJYH9CVqWtFvTrNOXN4+3tjbe3N1FRUeTW70pRy4fZm+Gm+93UQym3yi7Xjv/2CeGVPo1lfzfl7WapJ5wqX02+upQGfm66zAFy7ZCyeI7pHKR7qRnGXTB8yRrzvqrj40Ynx8v8+N5YQlq0uW1ZSi1BusdMLaaq8tUUqEt4tlt9uoX4mvSEkvj930wSfcLoPW46arWaNWvW8PLLL5OmyqXvSx/x8x/b7d6XaiVIHRwcKC0tZeLEiZgzHrC36Yf+6rIxlz9TD6qxdhmagpw9e5bly5eX+w3kBcTJkyf5/fffK90fuWPOHNyMyf1DWftsB9oH1aC+ryvzyhLcVWW80zZt2hAXFwfA6bSb5f6DdkFFisZvzGa1MqZpEh9uPUf0hSw+3HpOt03qm/SbnGA0bMfS6Ev0axFYLnOAoSDWP/fm2q6fVWDMymO6Y0jXY3Tn+rhd2kvcob12OQ/2xNg9pn8+JG3U39OVTx9rbdE9FtmhLpP7h3J049ek5+STE9SF2fMXsuFoCml1urHsRBG79x+xa1+q1dAeoEaNGgwbNgyNRmPS28FS8xtrpgCknES2pDWWM4XSH2K3bNmSffv2UVhYSFTcdZPTCf7+/nTpIuvNW2n027dufPnYDZWNJWoJnp6eFBUVAfDO4GY68yAJKeXMA40yK1gSWGJ1IWHs9+Z1anAgMZvmdWpU6Nu4bsE4Ozro2mMu9qbh9g+3nuNAYjbFpedY/kwHo22XQ9LEfdwdScws4KkVsTzRIYjxPW6ZNs2ePZv4+HiKTu/hzQd7V/mKvRyWPkeJGXmMWnGUnMJS8tVn+GJkWwCTJmSG95ihpq4/778vIYuDiSq2XSykb3kHQJuodoJUEASGDh1KZmamLiJMVXodlavbjmmNJQwfJEEQOHjwIEuXLuWzxcvK/WZIs2bNiI6Oxt3dnfz8fLsmF6uM6Zi1+0qkpqby1ltv6TyZ5Dy9TAkc6cEynEeVW1Azdr0n9GhAgKdLueMbE4zGjmHMk6llmZAuLhWJS87mfzFJTOzVkL0JWYgIDG1Ty+j5ktpwNbuAdbFXuVmoYcWBZPw8b9VVt25dRFEkKiqK1x7sJ3usqorVauqcyNU5f/sFcgq1HuYlGnSeinIptQ3PgbG5b9B6Nk7ZFE+qSputosDRvqEDq50gBfjuu+9IS0tj5kztKva3+y7z/YFkMvPUTB1guf+8ND/zUkQji97iUk4ie77x5TRnFxcXXnzxRT58dzrjxo3Dz0NewD/yyCO8/fbbbNmyhZ07d/LDDz/YzVjZ8MEwZ18rDb/0F7IkF0JzD66vry8nT57k0KFDRgMQWzLCMJxHtdRN1djx5UyopAc/X11KgbpEN9w0XHUuV3ePBlxIzyP6QhbjV5+goFhDsqqgLDaEdoFFX4gYGuyP796A6PMZ/Hj0KhoRfNwq3oP16tXjrdmfMPzTrZR4BCBqNDzS1J3c3FwKCwt56LVP8I4Yy8WLF/loTB+T57EyyJ1XufM/bWAT8tVnAIE2Qd7lnByMLUoZXgfD8xMZVofH/neItNwSXRljHm+VpVoK0ldeeYXCwkJdLpqzZXNpZ/Xm1CxBmp+xNFWxvXzYjXFLWLXBz8OFw4cPk5OTw5tvvsmMGTMIDAwsV97V1ZUhQ4ZQVFTErl27yMnJsUmQ6gtLwwfDEqsF/SGWsQhScgI5ISEBURQZM2aMyXLmMNQg5R5uY8LRknr0++jh4sjCnQm4u2gfMWnVWfpNPxiKtMA06KuD3CwqxdVR4KNhLcpppPqeenLRyeZvv4BGBCcHgaWj2lZo640bNxi14BdKvBoQ7OPMYx3r8cLY0XTq1Ilnn32Wnz+byrqDlyg9f4y8vAdwc3OzKhCIuTircs+G3PkPqempyxygytem7db3VDSmRYNWYTqbdpPiUg2xl3OACyx5qi1RcWk6IVrT05kSDXw+MszivllCtRSkBQUFfPXVV7zyyiuA/JyaJVhqZ1rVwyIJQ2H1+uuvI4oiMTExlJSUsGjRIpKTk1m4cKFun759+6LRaEhJSSEpKancar+17TflQmnuXBkKMWMh1OQE8s6dOxk9ejQNGzY0Wc5aV1xLtVhzUzumIkVJnwvUJYgIutiwcu2+WaQd0haViqw8kMzr/Rqz61wmvh7O5eqWi06mnx21fX0f3fbCwkJiYmLo1KkTXV2vUNgkTLcgt2zVGqLi0nCp4ccDtWvzQLtW5OT0ZNasWbQN70ZxcLjFUwCm5nmNYcn5V+Wp+XDrOd4Z3AygnIOCoQ3y9we00T4faOBDRBN/Xu0ZRK9evfhl605yijScTr3JB8NbExpoWT4va6iWgtTFxQWVSqXLmVSZaE5guYZp6TDXVuSElSAIuimMJ598kri4OLKysnj11VdZvXo1oLVmqFOnDs8++yynTp2qEE3cnLCwJP6luXNl6bmU6+NPP/3EypUrzbbFlusgFznMlBeYPsbOn/5nydvJMDas/v5OApSUTa/vOJNBUYlGdl5Q7n6W0t+cPXuWl16azhNPPME///xD3759mTNnDlu3bmXRnJnl9pFrt7e3Ny+++CIr9l/ml50JXE5K4oNRvUzuA+jmeVvqLcbZSlRcGivKhOMrP55kcOtaLIlO0k0J9WsWwJFLKt298Pe5dAQEXu8Xws97T9Kgbnt+/fVX6tcJ4O0hFc327Em1FKSOjo58+OGHulVeS7EmiZk+1g5zK4s5YVS3bl3q1q1LamoqPj4+fP/99wwbNgx/f3/Ce/TBNTyS3i/PZ+5zgxjSv0+F9j/cwo+kpKRymp+l/bH25WHJggzArl276NGjh06TNtUWW67D8n2XWXEgmehzGRy5fEO3jyUvAGPCVoritS8hm3kjtH71poa42+LTOJWqNV8a0KImr/drzAONMk0GkDakoKCAnJwcoqOjGTRoEGFhYURHR1col5iRx/6ETMZ1C67Q7iZNmjDgehaQi3DpEOnpLXTTRsb6Or5HA/wMFuPMYe6eiQyrgypPzc4z6SRmFiAiVIiqL1lqAMRezmFy/1D2nk1j44VSmhy/ysQ+VR9TGKqpIAX44IMPqF+/frl5NXNUNjK+4cNh6ZSAOSprqlO3bl0WL17MnDlzWLt2LeHh4fxTXAu3TpGUAH9dzKd2bCxqtZorV67QokULxH8PMfqdtXh7e9OlS5dyrpe2aGXGsPQcJSQk0Lt3b4v2M7wOhhqLhNx5k2xSReRdgCtDVFwa62JTAcr51RvzXpNWp6UXubRQ9fupaxVcRY1x/fp11qxZYzZgx/ztFziQmI2zo4PsvdW3e2f6dodJkzayYcMGJk2aBBh/mVdmfcDcPePn4cKUAU0Y36NBuVFIm3o1jE6fJKde49k+bfD19eWJ8OAKx6wqqq0gnTRpktVRs/XnnmwZnttr0cmaBRy539955x0EQWDevHmcSUzhpVGTERCpfeMMK1euJDo6mmeeeYbFixcTFhbGli1b+PHHH8nNzbW6P9a+PCw5ZnFxMQsWLGDVqlXl9pMzW5LDmG2p3HnTn0e3NmatsesQGVaHq9n57EvILudXb6y84ZBdKieXLkVuKsJNKGXq1KkcOXLEbB4mS7JAiKKIj48PEydONFrm0KFDNGvWrMJ0kSpfzfJ9lzl6WQUIdGrgq9VajdjVGiLXP8mhQX/xV//8ubs48VN8Li7CST6sAssDU1RbQert7c3Ro0fp1Ml47nBD9G9kU2kkbhfWLODIIWklU6dOxcHBgVmzZjFu3Di8Ax8kJ7ANj48eS+f2rXn++ec5duwYXl5epKWlkZCQQE5OjlXRxqvCYuHll19m0aJFdOvWrdx2Y4LI8OVnqJGaml+t7Dy6/nHkTKbefbiFrt6GAR4Wz7saHtfwhSE5f0gG5tnZKk5HfcU7c+bxf0dSzb5kLOlvaWkpzs7OJCcnV5juuXnzJp9//jlJSUk88MAD3Lhxg3PnzvHJl0v4+cQ1CtQluvlNgBNXbupsW/Wvk7F7Rt9ETt/uV/+/Pll5Rahu5vHag00Y271yzjC2UG0FqUqlYvHixaxYscJ4GRNap72G57ZgrwUcBwcHVPlqzjk2JPbkGW74Cyz86yKT+4fS19MTT09PnX3mzJkzOXLkCI8++ihPP/0048ePR6PR3JHo408//TTLli2jpKSE4cNvJa41dm2kh0/KF1WgLimnkVbF3LUlIxdTnjemMFWuqFi7wt84wJ0eoVr7yj1FQzhV6MM6O/XRycmJyZMnM3ToUL7++mvat29PamoqJSUlnDx5UpfFc/78+aSkpDBp0iQWbIhhy2VHXopoxHPdgstppIZz16bsiKWyhqZPxvr0zfZ/+OFkLjMGt8Dfs+osZ4xRbQVpUFAQa9asMbngZOrBqmqb0NtNVFwacWIjWqs9uLJxMZOfeEV2EUMQBJq2CaPnq1/y5aGzfNpjEAM6NWP8+PE0bNgQX1/fStUvJ3CkGKBFxaW4OjtWCJHWq1cvzp49S0RERLljmTKEB+1cmdyQuCrM2SxxC44M0wbqzleXGg3GbLjQafjdsE1SKhcfDxee7Vqf4cOHM3fuXPo0bka9IPOZOS3F0dGRESNG8MILLzB16lROnTpFcHAwoaGhfPzxxwBMmaKNd5ubm8umsX2Y9s0vPNahrtFzZ8qOGCrah5pDFEUmDe5IzcDk2zovqk+1FaQA8fHxLFiwgGXLlsn+fjdonXJY8iBbO4cr9dEj7QR12jTn8dY+bIzPlH2RRMWlseHEdXDzo17kDKY91YSBAwfSt29fvv76a5P1iKLI1atXCQoKKrdd7qUlzXlJeLg4lmuHo6Mj8fHxrFmzRmcTLNdvObMnuYDHci9HOUsNa6wUCou1ht6m3IL9PFxwd3Fi4c6ECn2UMFzoNPxu2KYxnbXn98zpfznglcOoUaMICQnB084KgKurK02aNOGnn36iQYMGDBs2rML8q7QW4e3tTfSOP1i1ahXdn1/FmjVrCAsLqzCaMWVHDJbFqNXnjTfeYPTo0bw2sLfs77eDai1ImzVrxptvvmn097tV67TkQbZ2mHqrrw0QRZHOnTvz+OhxTO7/SIWbWX+RZN6IFgQF+TJ16lS+++47hg0bxgcffEBRURErV66kffv25OTkEBUVRWBgIOHh4Rw+fJiAgACdHat0TIAR7WqTl5eHh4cHqz+YRINOj5Lr3YgOtZ0JvHmB9HR3nanNwoULad++PTVr1uTatWvUrl3bIrdOaX50/A/HScws5KNhzenW2F/2xSNnqWGNlcKkiIYVonXJ1WPumIaLP4bfI8PqcCW7gA1Hr9Ap2JuwYF9cHUR2p3vQLs+LF59+2mT9lcXZ2bnctIqpRSxBEKhbty6nTp2iT58+7N+/n88++4zVq1ebjbOrj9y5MnUtpk2bRs2aNa3tml0RbnOuOh3h4eFibGxsldeTnZ3N3r176devX5XXZS/spZGaKpObm8u+fftIT0+3yEQsLy+PM2fOkFcMs1fv4PFO9Qn0dicpKYmhQ4dy8eJFCgsLad++Pf/3f/+Hm5sbKSkpfPzxx3h5eZGdnY0gCKxfv5569eqxYMECJk6cyF/FzTiUnIdH9kX6Op6hc+fOPPHEE7z33nvExMQwb948YmNjOX36NNOnT2fPgViKgjqaFRQT157QLVK4OzswqXcIC3cm6ISedF6y84uNrtYbO3+mIrZLi5STIhqaTANiyTXS/y07v5jHv42loFhDSIA7T3ieIahxcxKpzRPhtzIuSPVP7h9625UEqb1da4mIhTf59ddfadGiBY8++miVtevy5ctcv369wvRPVSEIwlFRFCtE/6nWGiloVxe3bt16TwlSSzRlW90bvby88PT0ZNOmTYwZM4akpCSmTp2qy31liKenJ506dWL5viSu1uqCY9MQHuvZSPd7s2bNdJ/ff/99kpOTSUpK4pNPPsG3dn2W/nmcvo3c8XDU4Ovry8qVK2natCk9MvL474o9zHyuD075bfH392fChAkcOXKEL7/8ki5dutClSxdu3LjB/PnzcXZ25p0RQ0xqRqp8NU0CPbl6o4BkVRFzHtFqpHAr4pP+eTG2em3s/JmKwSBpTvnqUotGDKaukf5vRy6pKCjW4ObkwNTe9Vi+dCOUNOeDEbWt0nyrEqm92pdVMw7neCNcukJhYaFRm15jWKpZn7qay+zt1/i+oYqODW9vehF9qr0gDQ4O5quvvqKgoKBKsgreLj/7ymDuoerZsyfdunXj4YcfZvLkyQQHB3PixAkCAgJo2rSpbN8iO9QFQT7Fsj7+/v5MmTKFevXq4dmpE84PNMWhiT/vDmtBgJerrlxITU9+mzoEgHff/Z7S0lIWLlyIWq0uN1zz8fHh448/Zu7cuWbTREiuhYbajzR/mq8uNZupQP+8yU19yG2H8tGujKUBsfRY+ivXV7MLaODrRubWz/GM/JSEkOGorhYyf3t555GqmK6qTMjEqLg0zrq34oHGfgyf9hXFogNpNTvKxouVw9Kpqy+P5JCDB2/+FMeet/pa2TP7Ue2H9gDnz5/n2WefZft2+6cYuJNDKXtx6NAh2rZti4eHB4MHDyYyMpIJEybY1LcZM2bg5eVFfHw8nv61SQnqR1Kxl+yxLH1Qi4uLCW3Vnre/+5WRDwRXakpD6tNLEY10gs6SF2D0+Qym/fIvDzYPtDgamDV9tKTNAA8FlXKu0IvEzAL8PJxYPa6j1Q4E1lKZ+0DqT4G6hCXRSYQ5XcFZEJk1IVJnYA/yWWP195d+M2b1EfnZNq5pvPhP14bMHtHGvh2X4b4d2oPWd9iShHiVoTJDKWMBMu4U+lH0o6KicHXVaoy2DBPnzp0LwA8//EBiYiKD27mi8pN3vbRU+3B0dCTs0Yl88fdlnJydjZY15f2k705ozWLdjM2nuVFQSlRcmi4GqDXY4qXWo67AEf+b7N69hwZdX2TbQW3yvK+fbGsXIWqJz7v039KXnr5mrlEXciMujjcmTeDHExks/Ouirpyl5ofGrD6uabzoHOzJ6wOacSe5LwSpIAjEx8fToUMHatSwLDqNpQFMbPEx1vfauBu02Q8//JDExERdTih7DBOfeeYZs2UsFdg5OTncPLGdye+P0NnAGnsZmQuKIheFyRRzh7fUaaTWBBGR66PcvWVKWBXnZlN64lfefbQ3Q3qG4O3lbteXryU+79J2az3+/DxcGN+jIa/+ksiU5dtY98kMPvtpp8Ur8oZlpP8ZNwvZsWsPL3TvysQHW90RI3x97gtBCrB582aaNm1qkSBV5at55ceTZRHKrQtgYgn9mgWwLyGL+r5uFkffvx2MGjUKLy9trMbbOfdrqcD29fXl0SEDae9ynV3nMPkyMiecrX1JRDStyYG3tCvDlXEf1q9vyqZ4DiRmsy/hMMPb1WbygCY4lRYR7qkC6lRIh/L+uhi6tA7TWVcY07bB9oDX9iwr4enpSfcxU1i4M4FX5n7DgPra7da0UxplSNYSiCL/aIJ5yNP9jgtRqGZZRE3x5ZdfWhwdPioujcTMAkIC3K0OBm0Ju85lcjBRxdrYqxQVl94Vi1SrVq3iq6++onbt2oB9Mm9WBa6uruzevZvIMG2WzIm9GhLRxL/CarAkuKw5t5ZmPI0M06bW1k8lok9ccjZDFh8kLjkbjUbD8uXLdVo+oIvZWSpC1IlrbDiaQs+ePTl37hzvrdxaLq3Gp1H7OePWkjjXtrq6TF2bylw3U+dKCmATfyGJjzYdAqB21j/kq9LJzc3lu+++A+DSpUucPXsW0IbyM1x76RTsTSN/N3zU1xk5ciQ/Hta+jL7ek6g7V6ZIvp7FG+uPsyQ6iaXRl8jNvcmMwS0Y1fX2+9XLcd8I0tGjR1NcXGxRWekhXf1sxYl8e6QXjgyrQwM/N6B8SmFbsTS/uhwtWrQgPj6eo0eP6tpoazg5W8+V4f4ajUbn/y09/EeTtfmMdp3LrHQ7JSwVQn4eLgiILIlOYs3hlArtfHvLGRIzC3h7yxleffVVCgsL8fHxQRRFtm7dSp86xTSUBkZ5WXQOcmfXrl20atUK55SjeFzcg1POFS5cuMD2pbOIaOLPifRSXbuMXRtrc4wZY+PGjRw9ehS1Wk2rVq1wcXFhzf4E1sTn8+upDJKTkxFFEWdnZ1QqFW5ubsTFxXHs2DFcXFzo168fFy9e5MyZM4wdO1YbMGfDIS5lFXLRvQUhISFkH/+TwXUL+TMuicTMAiat+4cff/kNgL1793L8+HEcHBzo3r07xcXFLNpyiMMp+fQI9efxFh68OjiMF3uH3hXaKNxHQ/vIyEizZjMSpoZ99gh84efhwtJR7SqV/sQUlY2nChAaGoqzszMBAQF2G9bbeq4M9xcEgaioKMLDw2nf3nIvJEOKi4tRqVTUqlWL4uJinU2qNH/53b5LNA30IKKpcW8ZEUH337CdHw1rwWtrYxnkl45jq1b07duXjIwMevToQePGjWnQoAHZ+4/Qacxsjl71Z39SLpP6NiUgIIDP5s5m+vc72XLZEecDiezfs4PcYpGouDQ6BXszce0Jpg1sUiFNiX6mTcmqwBpLgeLcbN544w1++uknatWqha+vL76+vqSnpyMIAu89M5CWsVpfdv8+k3XHeP/99wFtgBmJEydOAFrNdO7cubi5uTG+oy8bE0Tee6Q1L/2YT6vQBsTFHaGfdxA7i5qQXVjKr/GZjBvlTl5eHk5OTri5ufHjjz/i5eXFR+MfIUyq/y4RnvrcF+ZPoiiSl5dnlwhG5oJJVBWW1FPZCP+gPUfPPfcc06dPZ/Guc+xMlzdVsqZttp4buf337dtHy5Yt8ff315XbsGEDfn5+uLq6EhMTwxtvvMGNGzf4+eefCQ4OZsiQIZSWluLk5IQoiuzcuZMlS5YwePBgNm3axLvvvkvPnj0B6PFpDKr8Evw8nNg3pZfu3Gg0mnLxbQ0Da0QdTyWyQ128XR3JyMjg7bff5tVXX6Vdu3a6fW7cuIG3tzeCIJCeno6Tp4/s+fn62+/5Ye958uN3cfDvnXh6au+zMSuPkZhZQEQT/wrhHqXI8RFN/HURlUyZLaWlpfFbQhGf/XWRnl7prJw2mv3795cLon27yMpTs+EuFpL6GDN/ui8E6dWrV+natStnzpyx+ViGN+ftsiO9XfVkZ2fToGkr3l3xO88/2NoiAXg7bWmnTZvG22+/XS5W6ptvvsmkSZM4d+4cN27c4MSZBHZfKqBbbXh0yEB++OEHQkND8fLyYvny5SxatIj9+/czc+ZM1Go169ev559//uHTTz8l+nwGMzafZu7wljqNdPz48YwcOZJjx46RnJzMl19+SUZGBoGBgQwfPpznn3+e33//ndDQUP744w+GDh3K1KlTgcq/TAoLCzmflsPi/alMG9iEXee0AWYMzZ5MmdLJ1S3FmX366ad59a2ZHMlwpFsdiOhiedze+5n7WpACFBUVUVJSgiAInDx5klatWpmNoG/MCNicoXBVcLvqWb9+Pdu2baN9+/Y8/vjj1K9f/65pG8Cnn35KzZo1CQoKYtmyZXzwwQckJiYyZMgQXZllMRdZtDtJJ9iLiopwcXFBEASKiop0drJS21fvS8BXdYakc/FERkbi6OiIt7c3tWrVYu3atVy9epVx48bh7u5OamoqS5cuZd++fXz88ccEBAQQFBSEv78/Dg4ObNu2jfDwcGrVqgXIv2QsPV9SvAB3ZwcKijUEeDrz1cg2hAX7lmv/msMpFBSX4ubsVMH3XyIxI4+5f57jxKr3+WfvDtRqdbnzoGAZ970gnTJlCrVr1+b111/noYceYu3atWYjwFcHryVL0X+4M64kMWvWLH744Qej0yEVhrZ2FKRyWqE+8+bNIy0tjdGjR/Pnn38SGhpabo7OGocH/WucHr2WjIwMNBoNERERXLhwgYsXLzJ79mwCAgJ0LsbFxcU4OTlVcDk+cOAAb7/9Nrt27SrXFsMAJ4b3VWJGHu//dgYBgVlDm+u0zcSMPMZ8fwxVfonueF1D/FjxTFiF9ks8FV6P9x5urn1BHEpBEKCF43Xe+/082R716drAi/WT7ly4uXud+16QlpSU4OjoqLv5x4wZQ//+/Rk5cqTRfaoiLujtxJq2SQ/kyz2D6OCWQffu3S0qP7l/KIBVLxxz7ZKbp9QnJycHBwcHvLy8iImJoUuXLri4VDyOJS9Ce1w/aW761Z5BOBVmlQvgItcOwzr1I1Xpz3+C1pTq6ZXHdd9dHQW+GNlG94JR5avp8ek+3e81XB35879dWXM4RZcwb/KDjeka7MGSA9d4Z2irKsnrfr9wX7uIAhVW7FesWGEyer6lD1hVpK+wF9a0TdIsa+Um8NVXy+jWrZvJIC9yq+XSZ3Pnzly75g5vqdNI5dAfSfTqVVHQmmqjIZXx3jLs3y1rCRjqncybb77Jxo0bcXNzk22HYZ3TBjYhX63VSLuG+NLx47/pFepH09pe5BdrytVdVCoyY/Np3QsmO78YV0eBolKtQnSzqJTHlx2htESrxXZr7Mfo7lozoZUtyuddUrAf941Gqs+xY8fYuXOnLuq6PtnZ2eTn57P1UolFWlZ10UhBa6d5/PhxOnbsaFOkLHOaYFWs5tsTc8eXG5rrW0vEHD7OxqMpTOjfljZNGxmtx3C/uORsRq88Xi7WfngDb2Iv5+i+uzjClyPbEtG0ZrmVfDmC/dzZ/ErPu34l/F7CmEZ63xjk61OzZk3atm2rm8tSq9WcPHmSxx57jCVLlrBt2zbCvPMtMkivjAeNtVhi2C5Xxtq2xcTE8Morr1QQosXFxaSmppKenk6vXr34888/TR7HnDG/Ne2S65etXlf6xzR1/DWHU2TPu2H/pIyc0tzmT0cusyPdi5iUYvbt28fp06dl2yFpsvO3XwC0hvySEK3h5kDrul6UaMorOu2DfHTD+m/3XSYxswBXR/kHuZ6vmyJEbxP3zdBenwYNGhAUFMQjjzxCrVq1GDVqFPHx8SxevJjGjRsDMHz4cEaNGsXO34to0qQJHTp0sKoOe2pNVZF6RI6IiAjdy0UURV599VVatWpF06ZNeeONN9i2bRvffvutbiX//PnzNG3atNwx7K0tWpJaxJZjAuUyj0aG1dEd11hwZv2hudxi0uxnBxMWl8bQVgHMmvEZTz/9tOx50U8nospX06G+N5dVBZRq4GahhvjU3AptD63lqUsauP3f6wAUlQIlanC6db5dHQU+jmxXYX+FquG+HNpLaDQaRFFEFMUKc6j5+fmsWbOGlJQUjh8/zvjx4xkwYIDFx7bniv/tWvTav38/O3fuJCEhgS+++IKUlBREUaRt27a6MhkZGfTu3Ztff/2VBQsWVMiL9fWeiyyJTmJSRENe6dO4Uu3Q75OxlB62HtfQ4kCKm2mtmZL+qrmxaz158mSSarTmX4cQxnULJsDTpcIxP9lxnpUHUsy2PbJ9bZJUhRy9fEO3rU4NZ9qLiRwRQ8jKK8bfw4nvxna+oxHjqyv3/WKTHKY8nTw8PJgwYQIApaWljBw5ku7du+PpaZnHkK1akz72Sj1ijqNHj9KxY0eaNWtGaWkpbdpUDJQbEBDA33//Tc2aNVmwYAElJSVkZmYSEKANGqLvOmkrplJ6mCMuOZu3t5zho2EtytldQsVzZSysntw5NRSukWHaVMsixrMGTJs2jU+3/su/SSJn025yIDEbuBXFqVOwNz/FXrWoXwcSVaTm3JpqcBNK+OP1Afh7DrRof4Wq4b7WSK1BrVZTUlJivuBdRGJGHh9uPUfzOjWY0MP2eVw5F9Q9e/bwzjvvsGnTJmrXrm3Xob0txxqy+KAugtfvL3e1qR36WDvSkPogXDnBog27+WXxbHafz9IJUclbydiCkSGN/d24mFUIaIfv617opmietxFlsckGjh07xsSJEwH7RH+6XczffoEDidl8fyDZLuHwDBdHQDuvGhUVxaOPPkppaanNdehjy0LeR8NaEBLgzkfDWhgtY+21tCbfk4QkLE9mOfDnoik0DvTS9UlatPpoWAtq17Csj/nqYl6IaEyP0AD+eD1CEaJ3Cff10N5SgoODmTZtGnB3240aMm1gE4pLtRqpPaYYDHOtg3Z6RK1WM378eERRvGvOT1iwr1lNVGqr/kKTodDW1+rdnR1ZGn3JqqmGyLA6JKbnEnXkItd+Os7iF2rq9tWfOljxTBhDlhw2eSwBWPJMF0V43oUoGqkFBAYGkp+fT0lJiV3idN4uQmp6svyZDkwd0MQuCzWGZj4A169f59FHH8XT0xMnJ6cqPz+VNQWTQ2qrgGjUnOr9387otHoB0WzfpJiwf5xKY8jigyRl5mtjpbp4EpfjwZrDFReUrl+/zrLfD5psq4eLAyvHPaAI0bsURSO1kKlTp/LNN99Qp06du14TvZ1OAoGBgWzdulWXOrkq0gHrY09TMGP5m8qv6msXzer7ujK6c32zqT2k6Y8jSdkUFGt4e8sZ+jYL4OcT1wAQxVtzzaVHN/De6y9SWlqKW2oc9Xy6c/VGYbnjD2pdm48j2yn2oHc5iiC1kG3btlFYWGi+4F3A7Rxer1+/nl27dvHtt9/qtlWlILfEGkKujKk2mcpY+cHQ5roFNnPurpFhdWgS6ElxqYbHOtRl8d+X+GhYCxoGeFDH243jx47RsYY/YxcdIsOlDu2bDKROnTr4+Pgwf3YrEtJz+fC3fxV/+HsQi1btBUEYBHwBOALfiaI4T6bMSGAWIAInRFF82rCMPvfaqv1ff/3FtWvXGDFixJ1uilluh92ptH+veo5oCm/SosWtRZ27JWqWfh+lIB6W2Ldac270y0pCVa7fe47+yyvLdxP17mgSEhL47YqrIjDvQSptRyoIgiOwGBgApABHBEHYIoriv3plmgIzgB6iKKoEQahlv6bfHdSqVeuuNH+Se+gtGV7bLQ1I/1DObV7JyJEj6dRJGxzYXja0tgp7/T4a2rdao6Fa2j5j/c7JyWHetvNo6rTii+gUVj7bi8es7o3C3YwlQ/vOwAVRFC8CCIKwHhgO/KtXZgKwWBRFFYAoitft3dA7Tdu2bWnSpEmF7IiWUJVD3eX7LrPiQDKqPDVTBlie/8lWYSft93ALPzTxTbhx45anjb3mSW0V9oZ9lFbm7XFsuWMYuo6u2H2ajn5F9O3emXCnJEJD+/HO0FaVqkvh7sYSQRoEJOt9TwG6GJRpBiAIwj60w/9ZoiiajmxxjyF5+uzevVu3sGIphvNo9hKqiRl5bDyu9Yj558oNqwS2rcLOz8OF1u459J+wEPe0OPZs+133m71eHLYKe6mP+o4EUnv0j12Z9pqzKY2KS2P50Sz21iimWzcnPps1vVJ9ULg3sJf5kxPQFOgDjAK+FQTB17CQIAgvCIIQKwhCbHp6up2qvj04OjoSFxdntRCF8tGCjEUuqoyh//ztF8gp1BrBtwvyue256P9MKkVsP4Kg/uP4+eef6dq1KwcOHGDwpNnadhxPtclIX98g39z5kftd2vbh1nMVHAn0j12Z8xYVl8bS6Eu4uzjJCl/frNN0rOPC2ZvObIhNljmCQnXCEo30ChCs971+2TZ9UoBDoigWA4mCIJxDK1iP6BcSRXEZsAy0i02VbfSdokaNGrz33nuMGTPGolxGEvranzEtqzJDzWkDm6AuPU/LOp6M73Frn9tl45p8Uxt0uGaTMJ55pgONGjWidevWjGgXS6pHHgfXLaK5+BiLFi1i48aNNtVlTquXO3/SAtOo8Ho4OzoYTX1dGc3X1D6qfDWxmU48HurAQx1a8ER4cIUyCtULs6v2giA4AeeAB9EK0CPA06IoxuuVGQSMEkVxrCAINYHjQJgoipnGjnuvrdpL/PXXXwQGBtKkif3y0YN951Fvlx2pqfTPmZmZnDx5krCwMNavX09WVhZvvfWWLoe8tZhbHZfr81d7ElkafYmXIhrx3z4htnW2DGN9TkhIoHbt2mRkZDBu/hqu1+3OjMEteLF3qF3qVbg7sClnkyAIDwOL0M5/rhBF8SNBEGYDsaIobhG0kYAXAoOAUuAjURTXmzrmvSpIRVGkoMCyABN3ClPmR6aEnz0wJsQ7dOjA+vXrad68uV3rAGQ/S9MBaw6nkF+swcPZoYJBfWXMxKT8SiEB7vwwrgPXkxMJCQkhMjKSOXPm0LlzZ+JOX+B4tus9kaddwTpsCqMniuIfwB8G297T+ywCb5b9VTtWr15N+/btiY6OxsHBgcTERGbNmnWnm2UUU8POW/mFLpRLsmYvjE1RHD9+nK+//prCwkLatzddrzFhr79d0kzz1aUsjb6kK2O4iu7u4sSSaO02dxcno4b3xjRbwzLTBjbh7LXjJGYWMGfLP1xc/yHbt29n9+7duuN2bt+Kzhaer6w8NRtikxWhe4+jeDZZgIeHB6tXr6ZXr17069ePjIyMO90kkxiuyEsR1QVEJvbSJkAzNl9oK6aEeM2aNdm7dy/t2rUzmRPKmLCfs/U8BxNVqEvP0yPUn4U7E5gU0bCC/7vh53x1KQJihTbJtdVQcEaG1SErr4i/z1zjRnwMk18YQ/bNfMCZw1eKOL5jh+UnR4YNscnM3XoGgCfCgxWheo+iCFIL6N27N3///Tf9+vUDqNTK/Z1k4Y4LRJX5eru7OFWJJiphyqzqqaeeAuDdd9+lVatWDBr+mOzQWlpECw30QJWv1v3Wso4nBxNVhAS46UyPDIfrku/88v2Xdcc1nB/V1zoN2yoJ3gJ1Cap8NSdjDxJ7TsPJTJGruPG2mxtL/9OFKZtO8tkTYTafr/6tahNzPp3MXDWr9ifyxV9aywJlbvXeQon+ZIYbN24Qc+IcCUEPkZiRd6ebYzWqfDW/n9L6R7g6Go/ifrs4efIkXbp0ITg42KjZUUhNT3qE+rPyQEq5357v0VAXwk4yPQKsTo5n6nc/Dxc8XBxZEp3E1E2nyCsRGNnMib7NA/lh8qMIgkC/VnU59u5A+rSw3YFv57/X2Hshk2UxFwGBGYOVVf57EUUjNUNMTAzz92aRSgDzt1fNvGJVEhWXpst57uXqeNvr19f+vF0dGT9+PLNnz6Znz560LhN+csFF+jULqPCbYbSmfs0CmPHLaV1Oeem3xPRcfN2daBroIdsmc+ZOkWF12Hs+nX2JN2gWWJv3I8N5xvZTIcsT4cHkq0sAgbHdGylD+nsURSM1Q//+/amTup/O9T2qbF6xKukU7I2rk/YyZ+aX3DZjfQmd9nc8ld27d7Nv3z4GDRoElJ8GkLRKqfyuc5lGo+NL++06l0n0hSwimviXW7WPOnGN7IISPv7zvGybzEXeX/f9d7QIdAXAu4a3zefAFP6eLrwxoDlvDGimCNF7GEUjNYObmxsvP/M4BQW5zJo8iVWrVt3pJhlFzixof0ImRSUafNwdeSysnt2H9uYyfUr1PRjiwcjhMzl4sGIAY0Nje/3/pkyU9Mvqu37uPJPOiZQcuof6mz2GPoWFhRQUFBAaGkrdRh7UC6y6YbayWl+9UASpBXTs2JFGjRqxefPmO90Uk8jla3+uW7DOq6cq7EalTJ+gDQpiuHijr3UePHhQNnOroUA0Z6Ikd2z9bUuealvhhWLOa0yVr+alBat5qksjnn7sUQDslzLvFpIAzVeX8sVfWo1ZWVi691EEqQV4enrSokULwsLC7nRTTCI391fV3k3G0hGLoohGoyE5OZnLly/Tq1cvunXrxr59+3ByKn/bSSHo5LRGS9039U28Rneuz/juDSgpKaG0tJRHWtdElaXN3JmYmIhGoyE0NJRVq1bxyCOPkJGRwZTv/uSCV1uG1DSeLM8erNp/iS/+Ok+XED9ee7CpsrBUTVDmSC3A1dWVTz75hOLi4jvdFJPoz/3ZkoHT2jpf6dOY//YJKVfX3LlzGTJkCIWFhXz11VcUFBTw5H/Gs+rwVdnAI4Yr6ZIJE2C2H3l5eQx/fR5Loy+xJDqJ8R9+h5OTE8OGDePo0aNQlMuOr6ZRx68Gx48f59ChQ7i5uaFSqXBxcaF+/fp8MG7wbVox1y78HUpU4eHiqAzrqwlKXnsLycnJoXv37uzduxcXF+XmN0VRURG5ubn4+fmVG8qbcl01nN+VVuONRdlX5atZ+PMBSs/H8Pm8D9m6K5pzYl1KSkoZ0yWYuv41qraTlSQrT82q/ZcAkbHdQxRBeo9hk4uoAnh7e7Nr1y5OnjypiwQvcTuTzZmjsmky7BIo5Xgqvqoz+Hu68Oqrr/LHH38QHHxLw7N0mB4Vl1ZhNV6/ratWreLvwoacvenM6LYDcHBwYEj/PgyxqQe3B+0qfbM73QwFO6MIUivYvHkzhYWFhIWF4eh4yybzbsnlbm1bKttuOQEcdTyVhX9dxPtiDCunjebEiRMVFpZMeT19u+8y3x9IJjNPzYQet0IOGobJu3HjBmLDzpyNzaJv80AmR4ZZ3G4FhapCEaRlnDt3jqCgIDw9ja9sT5gwgUceeQQ3NzdGjx6t226vHEWWYqlJkDksKWtYlypfzeRN/3IwUcX+hEw+faw1fh4uNCi9ituZrWz97kOrtVtVvppdZ7TxC86m3eTklRyW70siMT2XyQO0Ue0fbF6TTdEn8MvPZ1TkcGoFKqZDCncP1XKOVBRFrly5gkajIT09nePHjzN27Fh+/PFHHnvsMdzd3QHYv38/W7ZsITg4mNTUVDp00AYn/vfff9m0aRMLFy4kNTWVhIQExo4dy7Jly6hZsyb9+/evsPJc1VQmHqelxzNV3nBe8+s9F3XmTgDdQnzpEOzD6M718fd0NZnTylidUh1+Hk4sfrItE1afIK9YGzRaABq55uIn5nJMXYe+zQNZODJMEaAKd4T7Zo708OHDTHl7Fp0ef5nSC/t4csQQnaA8c+aM7kEXRZH33nuP//73v3Tv3l2niX755Zf06tWLlStX8uijjzJgwACGDh3K559/TmBgIEFBQbJCVN+1cde5TLvPl5oyWjf83ZJhuqXlDeuSsnCGN/DGydGRA4kqDiRmk3dDxaU/v2Pp0qVW19m6pguejhpU+SVs2HOc/GIN4AAaDaKDA4lFXgzq1Qqf6wXsPpvOhthkxfZS4a6i2gnSjh078tiUT/h89yUeqteBDh06EB8fj7+/PzNnzuTspRSe+/A7Nn8+HWdnZzp37lxuOP/qq6/qPv/666+6udDz58/TsWNH6tSRHwZLQuLIJRXRF7LIV5fi4eJoN8Fqymjd8HdrjydhSWrnMZ2DymXjlGw327mp8GzdutxxDPsuV+f36zbyU2YQeaUOhAZ68nBLV45lZpOUo4GyOdZ+zQOZ0EcbEHpDbDL9W9Xmm78TlKG9wl1DtROkTk5OPNOzKTdzcxnethbOzs68/PLL7Ny5kz0HYlkcm4OqQW82/3ONDRs2yHraSOgvKD388MMm65WEQ79mATzQKJMCdUk5wQq2LUSZy/ppbVZQOSN4S7RUw3r0Q9TVr+UHVHypSMe7dvkiYzs3Yfv27Rw7dozZs2fz27+ZXHIOIDTQk2X/CSc00IvGLXOZGfUPIPBxZFtCA710dTwRHsxLq2M5lKgiM0/NzIdbWtxnBYWqolrOkRrjndV7WH0qjz7NahL3zWTWrPgGV1dX/vrrL4Y+9mQFbczUPKK5OUY5rQwo531zp02lDOc/bTGHunTpEs888wwxMTFa//sDiXRu5Men285QdO0iq98ey9inHmPevHnUq1eP4uJigoKCrPY5/+bvBF0g5B6hAayZUBWOnAoK8tiUs6kquBOCVP+hdSotxNPTk6SkJHbs2IGmWT/mbzvHiEYiH47pw5YtW8gM7MDCvy7KLupIKS6MGYzLIQkugIgm/swd0fKOClN72ZHGxMRQv359Tp8+TUJCAq+99hrPPfccdfqMIeqi9v6yVyK4rDw1/9tzgX9Tb/LB8NbltFUFharmvllsMoW/p4vew6wVHI0bN+bFF18kK09Nbl4ej3cMorS0lO3bt7PgixEcO3aMnLht0H0CqampbDpbwJLoJJ7rFlwhxYU5pOjrccnZRF/IIiourUJKkNtpTG/tdIBcG36OSyPn1Bm8vLyIiIjA2dkZNzc31q5dS1aemuD9iYBgN9dLf08XZg5pZZdjKSjYi/tKI60M6enpFBYWEhQURLt27XhyziqWH0qjlSaRb6eOYVX0OYZ1asTu81k2mx6ZcqE0xJKyVeFxpX/MBRti2JzkoKQdVrhvUDTSShIYGKj7fPLkSbLy1Hi6OtPJvza/xmey7HA6BxMz+Sddw9UrV3j3iW5cy87jt/gMwnwKaFA7oNwxwLgmaKsxvbkMmIZURtDqH/OJ8GDq1StVIhgp3Pco0Z+sQBAEArxcefPhdvTuGs7I8GCmD2rOZ8/05Jm2XuSc2I6LiwsTPl7Owr8usviPYxw+fBhRFJk5cyYajYbUrJt8ty9JNgKS/kq63O+GZQ2jIhlGUIoMq2Ny+sFcbiMJKRKTKl9NqHCNsWE+tPeHSZ/8HxP6t1VMkBTuexSN1Ab8PV2Y2EebfmTO6N4wujcAP81/k59ikxkZPgB/TxcKCgpo3rw5Hh4ezPpiEzE3a+Lo4IDThT0MGzYMb29vXXpiW/z2DbVUc3OglrqIvvbTKWIv3+D3Y4n0djpPt1bNOHWjFjmNH1SM4xUUUOZIbztZeWp+PHKZJx9owKcfzeKtt95i1apVnDp1ijfem8sHv52mdZAPz/doWCUr+pYO5+OSs5n+y2lquDkRn5qr2/5AkAcb/ttXSZWhcF+izJHeJfh7uvBSmRb78ccfA9pgKCqVind3JHL48k0cEHEsqY1aTaVin5oSlpbOm64+lMy1mxWnF2Kv5Ov6oWiiCgpaFEF6F+Dp6YmnpyfvDPVFoznFu4+0ZvPPa0lISGDWrFlWHUuVr66Qolgfc8N5SdA6ycyeC8Ab/e+9TKoKClWNIkjvIkIDvVg1Xuup0+TFFykpKeGHH37g+PHjzJs3z6JjyAVFlkjMyGP+9gu6tNLL91+uoLV2CvbGz8MJVX5JuX3dHOHMR/dC6GQFhduPIkjvYpycnHjiiScYMGAAp5Mz+SImxWw2ULkUxRLzt18o01Qv8EAjv3LRpKLi0hjYxIuvdiWiyi/B3UFDgcYBjUaDg4MDhaVV1k0FhXseRZDe5Xh5eaHRaOg+fQkO9dsCF/jf0+2Nlje1Uq/VRLUaqa+HM/nqUgrUJazan8Sy/Sls3HCMCUO6Ijj48FDdEgq865Oek8+K/cnKkF5BwQTKqv09wj+XrvHSNzt4uG4hbzz/tF2OKXlHNVWfx1kQ+W7mc9Txq2EyIpaCwv2Msmp/j9OuUW3+mP4IJSUl7Ny5kzoNm3Ao3aHS7p8ajQaXlKOE3LjE+k+mEODlWgWtVlC4P1AE6T2Ej48PAMeOHcNJXYvvYjNBFBnfo6HFx1Dlq1mz/yJfTx7NydiDPPUo+ChCVEHBJpQx3D3IrFmz8Lx+kh6e14lZv4Q3vvmd9JwCk/mSJF7+dDVL9qcy89tf8fHx0QlnBQWFyqPMkd6jXL9+ndLSUqL+zeGTHRcId0rm4cYuPPXUU7Lls7KySEpKIjO3iDNqP57v11rxSFJQsBJljrSaUatWLQBGeQfg5OREc5faJF84TX5+Ph4eHuTn57N69WpefPFFpkyZQseOHdmxYwfr169n2B1uu4JCdUMZ2t/jSK6aB/bs4MCBA0RERBAVFcWGDRvIzc3F3d2d5557jkcffZR169bd6eYqKFRLlKF9NaKkpARHR0eOHj1KQUEBvXr1utNNUlCoVihD+/sAJyft5QwPr3CdFRQUqhBlaK+goKBgI4ogVVBQULARRZAqKCgo2IgiSBUUFBRsRBGkCgoKCjaiCFIFBQUFG1EEqYKCgoKNKIJUQUFBwUYsEqSCIAwSBOGsIAgXBEGYLvP7OEEQ0gVBiCv7e97+TVVQUFC4OzHr2SQIgiOwGBgApABHBEHYIorivwZFfxRF8ZUqaKOCgoLCXY0lGmln4IIoihdFUVQD64HhVdssBQUFhXsHSwRpEJCs9z2lbJshjwmC8I8gCBsFQQiWO5AgCC8IghArCEJsenp6JZqroKCgcPdhr8WmX4FGoii2A3YAq+QKiaK4TBTFcFEUwwMDA+1UtYKCgsKdxRJBegXQ1zDrl23TIYpipiiKRWVfvwM62ad5CgoKCnc/lgjSI0BTQRBCBEFwAZ4CtugXEAShrt7XYcBp+zVRQUFB4e7G7Kq9KIolgiC8AmwDHIEVoijGC4IwG4gVRXEL8KogCMOAEiALGFeFbVZQUFC4q1Ai5CsoKChYiLEI+Ypnk4KCgoKN3DGNVBCEdCDpDlRdE8i4A/XeDqpz36B696869w2qT/8aiqJYweTojgnSO4UgCLFyqnl1oDr3Dap3/6pz36D6908Z2isoKCjYiCJIFRQUFGzkfhSky+50A6qQ6tw3qN79q859g2rev/tujlRBQUHB3tyPGqmCgoKCXbmnBKkgCJcEQThZFjw6tmybvyAIOwRBOF/2369suyAIwpdlwaj/EQSho95xxpaVPy8Iwli97Z3Kjn+hbF/BVB127ptvWeSsM4IgnBYEoVs16ltzvaDfcYIg5AiC8Ho16t8bgiDEC4JwShCEdYIguJW5VB8qa8+PZe7VCILgWvb9QtnvjfSOM6Ns+1lBEB7S2y4bWN1YHVXQv9fK+hYvCMLrZduqxbWzG6Io3jN/wCWgpsG2BcD0ss/Tgfllnx8GtgIC0BU4VLbdH7hY9t+v7LNf2W+Hy8oKZfsONlWHnfu2Cni+7LML4Ftd+mbQT0cgDWhYHfqHNqRkIuBe9v0ntC7SPwFPlW37H/BS2edJwP/KPj+FNiA6QCvgBOAKhAAJZefKsexz47L74gTQSq+uCnXYuX9tgFOAB1qX8p1Ak+pw7ex6nu50A6y8qJeoKEjPAnXLPtcFzpZ9/gYYZVgOGAV8o7f9m7JtdYEzett15YzVYcd++ZQ9jEJ165tMXwcC+6pL/7gVr9cfraD5DXgIrfG5U1mZbsC2ss/bgG5ln53KygnADGCG3nG3le2n27ds+4yyP8FYHXbu3xPAcr3v7wJTq8O1s+ffPTW0B0RguyAIRwVBeKFsW21RFFPLPqcBtcs+GwtIbWp7isx2U3XYixAgHVgpCMJxQRC+EwTB00S991LfDHkKWGem7numf6IoXgE+BS4DqcAN4CiQLYpiiUx7dH0o+/0GEID1fQ4wUYc9OQX0EgQhQBAED7QaZzDV4NrZk3tNkPYURbEjMBh4WRCECP0fRe2rq0rNEKqoDiegI7BUFMUOQB7aoUxV11uOqq6jbA5vGLDhdtddVXWUzdsNR/syrAd4AoPsWcedRBTF08B8YDvwJxAHlBqUuSevnT25pwRp2dsfURSvAz+jzSd1TSiLh1r2/3pZcWMBqU1try+zHRN12IsUIEUUxUNl3zeiFazVoW/6DAaOiaJ4zUzd91L/+gOJoiimi6JYDEQBPQBfQRCkMJX67dH1oex3HyAT6/ucaaIOuyKK4nJRFDuJohgBqIBzVI9rZzfuGUEqCIKnIAg1pM9o59pOoQ0yLa0AjgU2l33eAvynbBWxK3CjbJiwDRgoCIJfmTYxEO3cUiqQIwhC17JVw/8YHEuuDrsgimIakCwIQvOyTQ8C/1aHvhkwilvDelN130v9uwx0FQTBo6xu6drtBh430jepPY8Du8q0rS3AU2Wr+iFAU7SLMLKB1cv2MVaHXREEoVbZ/wZAJLCW6nHt7MednqS19A/tquWJsr944O2y7QHAX8B5tCuK/mXbBbRppBOAk0C43rGeAy6U/T2rtz0crXBOAL7mlsOCbB127l8YEAv8A/yCdmWzWvStrB5PtFqUj962atE/4APgTFn9P6BdeW+MVhBeQDuV4VpW1q3s+4Wy3xvrHeftsvafpWzlumz7w2i1wATpvtd7JirUUQX9i0H7cjgBPFidrp29/hTPJgUFBQUbuWeG9goKCgp3K4ogVVBQULARRZAqKCgo2IgiSBUUFBRsRBGkCgoKCjaiCFIFBQUFG1EEqYKCgoKNKIJUQUFBwUb+HxrqEAuuzXs+AAAAAElFTkSuQmCC",
      "text/plain": [
       "<Figure size 576x576 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "# Create a map\n",
    "ax = regions.plot(figsize=(8,8), color='whitesmoke', linestyle=':', edgecolor='black')\n",
    "facilities.to_crs(epsg=32630).plot(markersize=1, ax=ax) # 32630으로 통일시켜줌."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Region</th>\n",
       "      <th>geometry</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>POLYGON ((686446.075 842986.894, 686666.193 84...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>Brong Ahafo</td>\n",
       "      <td>POLYGON ((549970.457 968447.094, 550073.003 96...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>Central</td>\n",
       "      <td>POLYGON ((603176.584 695877.238, 603248.424 69...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>Eastern</td>\n",
       "      <td>POLYGON ((807307.254 797910.553, 807311.908 79...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>Greater Accra</td>\n",
       "      <td>POLYGON ((858081.638 676424.913, 858113.115 67...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>Northern</td>\n",
       "      <td>POLYGON ((818287.468 1185632.455, 818268.664 1...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>Upper East</td>\n",
       "      <td>POLYGON ((811994.328 1230449.528, 812004.699 1...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>Upper West</td>\n",
       "      <td>POLYGON ((658854.315 1220818.656, 659057.210 1...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>Volta</td>\n",
       "      <td>POLYGON ((899718.788 875120.098, 899564.444 87...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>Western</td>\n",
       "      <td>POLYGON ((490349.315 771271.143, 490530.091 77...</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "          Region                                           geometry\n",
       "0        Ashanti  POLYGON ((686446.075 842986.894, 686666.193 84...\n",
       "1    Brong Ahafo  POLYGON ((549970.457 968447.094, 550073.003 96...\n",
       "2        Central  POLYGON ((603176.584 695877.238, 603248.424 69...\n",
       "3        Eastern  POLYGON ((807307.254 797910.553, 807311.908 79...\n",
       "4  Greater Accra  POLYGON ((858081.638 676424.913, 858113.115 67...\n",
       "5       Northern  POLYGON ((818287.468 1185632.455, 818268.664 1...\n",
       "6     Upper East  POLYGON ((811994.328 1230449.528, 812004.699 1...\n",
       "7     Upper West  POLYGON ((658854.315 1220818.656, 659057.210 1...\n",
       "8          Volta  POLYGON ((899718.788 875120.098, 899564.444 87...\n",
       "9        Western  POLYGON ((490349.315 771271.143, 490530.091 77..."
      ]
     },
     "execution_count": 12,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "regions"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Region</th>\n",
       "      <th>District</th>\n",
       "      <th>FacilityName</th>\n",
       "      <th>Type</th>\n",
       "      <th>Town</th>\n",
       "      <th>Ownership</th>\n",
       "      <th>Latitude</th>\n",
       "      <th>Longitude</th>\n",
       "      <th>geometry</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Offinso North</td>\n",
       "      <td>A.M.E Zion Clinic</td>\n",
       "      <td>Clinic</td>\n",
       "      <td>Afrancho</td>\n",
       "      <td>CHAG</td>\n",
       "      <td>7.40801</td>\n",
       "      <td>-1.96317</td>\n",
       "      <td>POINT (-1.96317 7.40801)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Bekwai Municipal</td>\n",
       "      <td>Abenkyiman Clinic</td>\n",
       "      <td>Clinic</td>\n",
       "      <td>Anwiankwanta</td>\n",
       "      <td>Private</td>\n",
       "      <td>6.46312</td>\n",
       "      <td>-1.58592</td>\n",
       "      <td>POINT (-1.58592 6.46312)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Adansi North</td>\n",
       "      <td>Aboabo Health Centre</td>\n",
       "      <td>Health Centre</td>\n",
       "      <td>Aboabo No 2</td>\n",
       "      <td>Government</td>\n",
       "      <td>6.22393</td>\n",
       "      <td>-1.34982</td>\n",
       "      <td>POINT (-1.34982 6.22393)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Afigya-Kwabre</td>\n",
       "      <td>Aboabogya Health Centre</td>\n",
       "      <td>Health Centre</td>\n",
       "      <td>Aboabogya</td>\n",
       "      <td>Government</td>\n",
       "      <td>6.84177</td>\n",
       "      <td>-1.61098</td>\n",
       "      <td>POINT (-1.61098 6.84177)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Kwabre</td>\n",
       "      <td>Aboaso Health Centre</td>\n",
       "      <td>Health Centre</td>\n",
       "      <td>Aboaso</td>\n",
       "      <td>Government</td>\n",
       "      <td>6.84177</td>\n",
       "      <td>-1.61098</td>\n",
       "      <td>POINT (-1.61098 6.84177)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>...</th>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3751</th>\n",
       "      <td>Western</td>\n",
       "      <td>Sefwi-Akontombra</td>\n",
       "      <td>Ackaakrom CHPS</td>\n",
       "      <td>CHPS</td>\n",
       "      <td>Ackaakrom</td>\n",
       "      <td>Government</td>\n",
       "      <td>NaN</td>\n",
       "      <td>NaN</td>\n",
       "      <td>POINT EMPTY</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3752</th>\n",
       "      <td>Western</td>\n",
       "      <td>Sefwi-Akontombra</td>\n",
       "      <td>Apprutu CHPS</td>\n",
       "      <td>CHPS</td>\n",
       "      <td>Apprutu</td>\n",
       "      <td>Government</td>\n",
       "      <td>NaN</td>\n",
       "      <td>NaN</td>\n",
       "      <td>POINT EMPTY</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3753</th>\n",
       "      <td>Western</td>\n",
       "      <td>Sefwi-Akontombra</td>\n",
       "      <td>Kojokrom CHPS</td>\n",
       "      <td>CHPS</td>\n",
       "      <td>Kojokrom</td>\n",
       "      <td>Government</td>\n",
       "      <td>NaN</td>\n",
       "      <td>NaN</td>\n",
       "      <td>POINT EMPTY</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3754</th>\n",
       "      <td>Western</td>\n",
       "      <td>Sefwi-Akontombra</td>\n",
       "      <td>Yawkrom CHPS</td>\n",
       "      <td>CHPS</td>\n",
       "      <td>Yawkrom</td>\n",
       "      <td>Government</td>\n",
       "      <td>NaN</td>\n",
       "      <td>NaN</td>\n",
       "      <td>POINT EMPTY</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3755</th>\n",
       "      <td>Western</td>\n",
       "      <td>Sefwi-Akontombra</td>\n",
       "      <td>Kofikrom CHPS</td>\n",
       "      <td>CHPS</td>\n",
       "      <td>Kofikrom</td>\n",
       "      <td>Government</td>\n",
       "      <td>NaN</td>\n",
       "      <td>NaN</td>\n",
       "      <td>POINT EMPTY</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>3756 rows × 9 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "       Region          District             FacilityName           Type  \\\n",
       "0     Ashanti     Offinso North        A.M.E Zion Clinic         Clinic   \n",
       "1     Ashanti  Bekwai Municipal        Abenkyiman Clinic         Clinic   \n",
       "2     Ashanti      Adansi North     Aboabo Health Centre  Health Centre   \n",
       "3     Ashanti     Afigya-Kwabre  Aboabogya Health Centre  Health Centre   \n",
       "4     Ashanti            Kwabre     Aboaso Health Centre  Health Centre   \n",
       "...       ...               ...                      ...            ...   \n",
       "3751  Western  Sefwi-Akontombra          Ackaakrom CHPS            CHPS   \n",
       "3752  Western  Sefwi-Akontombra             Apprutu CHPS           CHPS   \n",
       "3753  Western  Sefwi-Akontombra            Kojokrom CHPS           CHPS   \n",
       "3754  Western  Sefwi-Akontombra             Yawkrom CHPS           CHPS   \n",
       "3755  Western  Sefwi-Akontombra            Kofikrom CHPS           CHPS   \n",
       "\n",
       "              Town   Ownership  Latitude  Longitude                  geometry  \n",
       "0         Afrancho        CHAG   7.40801   -1.96317  POINT (-1.96317 7.40801)  \n",
       "1     Anwiankwanta     Private   6.46312   -1.58592  POINT (-1.58592 6.46312)  \n",
       "2      Aboabo No 2  Government   6.22393   -1.34982  POINT (-1.34982 6.22393)  \n",
       "3        Aboabogya  Government   6.84177   -1.61098  POINT (-1.61098 6.84177)  \n",
       "4           Aboaso  Government   6.84177   -1.61098  POINT (-1.61098 6.84177)  \n",
       "...            ...         ...       ...        ...                       ...  \n",
       "3751     Ackaakrom  Government       NaN        NaN               POINT EMPTY  \n",
       "3752       Apprutu  Government       NaN        NaN               POINT EMPTY  \n",
       "3753      Kojokrom  Government       NaN        NaN               POINT EMPTY  \n",
       "3754       Yawkrom  Government       NaN        NaN               POINT EMPTY  \n",
       "3755      Kofikrom  Government       NaN        NaN               POINT EMPTY  \n",
       "\n",
       "[3756 rows x 9 columns]"
      ]
     },
     "execution_count": 13,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "facilities"
   ]
  },
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The `to_crs()` method modifies only the \"geometry\" column: all other columns are left as-is."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Region</th>\n",
       "      <th>District</th>\n",
       "      <th>FacilityName</th>\n",
       "      <th>Type</th>\n",
       "      <th>Town</th>\n",
       "      <th>Ownership</th>\n",
       "      <th>Latitude</th>\n",
       "      <th>Longitude</th>\n",
       "      <th>geometry</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Offinso North</td>\n",
       "      <td>A.M.E Zion Clinic</td>\n",
       "      <td>Clinic</td>\n",
       "      <td>Afrancho</td>\n",
       "      <td>CHAG</td>\n",
       "      <td>7.40801</td>\n",
       "      <td>-1.96317</td>\n",
       "      <td>POINT (614422.662 818986.851)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Bekwai Municipal</td>\n",
       "      <td>Abenkyiman Clinic</td>\n",
       "      <td>Clinic</td>\n",
       "      <td>Anwiankwanta</td>\n",
       "      <td>Private</td>\n",
       "      <td>6.46312</td>\n",
       "      <td>-1.58592</td>\n",
       "      <td>POINT (656373.863 714616.547)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Adansi North</td>\n",
       "      <td>Aboabo Health Centre</td>\n",
       "      <td>Health Centre</td>\n",
       "      <td>Aboabo No 2</td>\n",
       "      <td>Government</td>\n",
       "      <td>6.22393</td>\n",
       "      <td>-1.34982</td>\n",
       "      <td>POINT (682573.395 688243.477)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Afigya-Kwabre</td>\n",
       "      <td>Aboabogya Health Centre</td>\n",
       "      <td>Health Centre</td>\n",
       "      <td>Aboabogya</td>\n",
       "      <td>Government</td>\n",
       "      <td>6.84177</td>\n",
       "      <td>-1.61098</td>\n",
       "      <td>POINT (653484.490 756478.812)</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>Kwabre</td>\n",
       "      <td>Aboaso Health Centre</td>\n",
       "      <td>Health Centre</td>\n",
       "      <td>Aboaso</td>\n",
       "      <td>Government</td>\n",
       "      <td>6.84177</td>\n",
       "      <td>-1.61098</td>\n",
       "      <td>POINT (653484.490 756478.812)</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "    Region          District             FacilityName           Type  \\\n",
       "0  Ashanti     Offinso North        A.M.E Zion Clinic         Clinic   \n",
       "1  Ashanti  Bekwai Municipal        Abenkyiman Clinic         Clinic   \n",
       "2  Ashanti      Adansi North     Aboabo Health Centre  Health Centre   \n",
       "3  Ashanti     Afigya-Kwabre  Aboabogya Health Centre  Health Centre   \n",
       "4  Ashanti            Kwabre     Aboaso Health Centre  Health Centre   \n",
       "\n",
       "           Town   Ownership  Latitude  Longitude  \\\n",
       "0      Afrancho        CHAG   7.40801   -1.96317   \n",
       "1  Anwiankwanta     Private   6.46312   -1.58592   \n",
       "2   Aboabo No 2  Government   6.22393   -1.34982   \n",
       "3     Aboabogya  Government   6.84177   -1.61098   \n",
       "4        Aboaso  Government   6.84177   -1.61098   \n",
       "\n",
       "                        geometry  \n",
       "0  POINT (614422.662 818986.851)  \n",
       "1  POINT (656373.863 714616.547)  \n",
       "2  POINT (682573.395 688243.477)  \n",
       "3  POINT (653484.490 756478.812)  \n",
       "4  POINT (653484.490 756478.812)  "
      ]
     },
     "execution_count": 14,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# The \"Latitude\" and \"Longitude\" columns are unchanged\n",
    "facilities.to_crs(epsg=32630).head()"
   ]
  },
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "In case the EPSG code is not available in GeoPandas, we can change the CRS with what's known as the \"proj4 string\" of the CRS. For instance, the proj4 string to convert to latitude/longitude coordinates is as follows:\n",
    "\n",
    "+proj=longlat +ellps=WGS84 +datum=WGS84 +no_defs \n",
    "\n",
    ":위,경도 좌표계로 바꾸는 코드"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Region</th>\n",
       "      <th>geometry</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>POLYGON ((-1.30985 7.62302, -1.30786 7.62198, ...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>Brong Ahafo</td>\n",
       "      <td>POLYGON ((-2.54567 8.76089, -2.54473 8.76071, ...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>Central</td>\n",
       "      <td>POLYGON ((-2.06723 6.29473, -2.06658 6.29420, ...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>Eastern</td>\n",
       "      <td>POLYGON ((-0.21751 7.21009, -0.21747 7.20993, ...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>Greater Accra</td>\n",
       "      <td>POLYGON ((0.23456 6.10986, 0.23484 6.10974, 0....</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "          Region                                           geometry\n",
       "0        Ashanti  POLYGON ((-1.30985 7.62302, -1.30786 7.62198, ...\n",
       "1    Brong Ahafo  POLYGON ((-2.54567 8.76089, -2.54473 8.76071, ...\n",
       "2        Central  POLYGON ((-2.06723 6.29473, -2.06658 6.29420, ...\n",
       "3        Eastern  POLYGON ((-0.21751 7.21009, -0.21747 7.20993, ...\n",
       "4  Greater Accra  POLYGON ((0.23456 6.10986, 0.23484 6.10974, 0...."
      ]
     },
     "execution_count": 15,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Change the CRS to EPSG 4326\n",
    "regions.to_crs(\"+proj=longlat +ellps=WGS84 +datum=WGS84 +no_defs\").head()"
   ]
  },
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Attributes of geometric objects\n",
    "As you learned in the first tutorial, for an arbitrary GeoDataFrame, the type in the \"geometry\" column depends on what we are trying to show: for instance, we might use:\n",
    "\n",
    "- a Point for the epicenter of an earthquake,\n",
    "- a LineString for a street, or\n",
    "- a Polygon to show country boundaries.\n",
    "\n",
    "All three types of geometric objects have built-in attributes that you can use to quickly analyze the dataset. For instance, you can get the x- and y-coordinates of a Point from the x and y attributes, respectively."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0   -1.96317\n",
       "1   -1.58592\n",
       "2   -1.34982\n",
       "3   -1.61098\n",
       "4   -1.61098\n",
       "dtype: float64"
      ]
     },
     "execution_count": 16,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Get the x-coordinate of each point\n",
    "facilities.geometry.head().x"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0       7.40801\n",
       "1       6.46312\n",
       "2       6.22393\n",
       "3       6.84177\n",
       "4       6.84177\n",
       "         ...   \n",
       "3751        NaN\n",
       "3752        NaN\n",
       "3753        NaN\n",
       "3754        NaN\n",
       "3755        NaN\n",
       "Length: 3756, dtype: float64"
      ]
     },
     "execution_count": 17,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "facilities.geometry.y"
   ]
  },
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "And, you can get the length of a LineString from the length attribute.\n",
    "\n",
    "Or, you can get the area of a Polygon from the area attribute."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Area of Ghana: 239584.5760055668 square kilometers\n",
      "CRS: epsg:32630\n"
     ]
    },
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Region</th>\n",
       "      <th>geometry</th>\n",
       "      <th>AREA</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>Ashanti</td>\n",
       "      <td>POLYGON ((686446.075 842986.894, 686666.193 84...</td>\n",
       "      <td>24379.017777</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>Brong Ahafo</td>\n",
       "      <td>POLYGON ((549970.457 968447.094, 550073.003 96...</td>\n",
       "      <td>40098.168231</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>Central</td>\n",
       "      <td>POLYGON ((603176.584 695877.238, 603248.424 69...</td>\n",
       "      <td>9665.626760</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>Eastern</td>\n",
       "      <td>POLYGON ((807307.254 797910.553, 807311.908 79...</td>\n",
       "      <td>18987.625847</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>Greater Accra</td>\n",
       "      <td>POLYGON ((858081.638 676424.913, 858113.115 67...</td>\n",
       "      <td>3706.511145</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "          Region                                           geometry  \\\n",
       "0        Ashanti  POLYGON ((686446.075 842986.894, 686666.193 84...   \n",
       "1    Brong Ahafo  POLYGON ((549970.457 968447.094, 550073.003 96...   \n",
       "2        Central  POLYGON ((603176.584 695877.238, 603248.424 69...   \n",
       "3        Eastern  POLYGON ((807307.254 797910.553, 807311.908 79...   \n",
       "4  Greater Accra  POLYGON ((858081.638 676424.913, 858113.115 67...   \n",
       "\n",
       "           AREA  \n",
       "0  24379.017777  \n",
       "1  40098.168231  \n",
       "2   9665.626760  \n",
       "3  18987.625847  \n",
       "4   3706.511145  "
      ]
     },
     "execution_count": 18,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Calculate the area (in square meters) of each polygon in the GeoDataFrame \n",
    "regions.loc[:, \"AREA\"] = regions.geometry.area / 10**6  # .area : 면적 계산 코드 / 제곱킬로미터(1000 x 1000)로 나타내기 위해 10의 6승으로 나눠줌.\n",
    "\n",
    "print(\"Area of Ghana: {} square kilometers\".format(regions.AREA.sum()))\n",
    "print(\"CRS:\", regions.crs)\n",
    "regions.head()"
   ]
  },
  {
   "attachments": {},
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "In the code cell above, since the CRS of the regions GeoDataFrame is set to EPSG 32630 (a \"Mercator\" projection), the area calculation is slightly less accurate than if we had used an equal-area projection like \"Africa Albers Equal Area Conic\".\n",
    "\n",
    "But this yields the area of Ghana as approximately 239585 square kilometers, which is not too far off from the correct answer."
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "base",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.9.7"
  },
  "orig_nbformat": 4
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
