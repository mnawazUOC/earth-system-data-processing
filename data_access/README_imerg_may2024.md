README – IMERG Global Precipitation (May 2024)

Author: Mohammed Fawaz Nawaz
Course: Earth System Data Processing 1 (ESDP1), WS 2025/26
Dataset: NASA GPM IMERG Half-Hourly (GPM_3IMERGHH.07)
Time Period: 1–3 May 2024
Notebook: load_imerg_mnawaz_may2024.ipynb

1. Introduction

This README documents the process of identifying, accessing, and downloading the IMERG Half-Hourly precipitation dataset for Homework 1. IMERG (Integrated Multi-satellitE Retrievals for GPM) is produced by NASA’s Global Precipitation Measurement (GPM) mission and provides global precipitation estimates every 30 minutes at ~0.1° spatial resolution.

For the assignment, I selected May 1–3, 2024, fulfilling the requirement to process more than one day and implement a date loop with simple date arithmetic.

The dataset is openly documented but requires NASA Earthdata authentication to download from the official NASA GES DISC archive.

2. Dataset Description

Provider: NASA GES DISC (Goddard Earth Sciences Data and Information Services Center)

Product: IMERG Half-Hourly

Version: V07B

Format: HDF5 (.HDF5)

Temporal Resolution: 30 minutes

Spatial Resolution: ~0.1°

Coverage: Global

Important IMERG Variables

precipitationCal – Gauge-calibrated precipitation rate

precipitationUncal – Raw uncalibrated estimate

RandomError – Uncertainty estimate

Official Dataset Page

https://disc.gsfc.nasa.gov/datasets/GPM_3IMERGHH_07/summary

3. Data Portal Evaluation & File Structure

To access IMERG, I explored the NASA GES DISC portal. Key findings:

Directory Structure

IMERG files are stored using a hierarchical structure:

/GPM_L3/GPM_3IMERGHH.07/YYYY/DOY/


Where:

YYYY = year

DOY = day of year (e.g., 122 = May 1, 2024)

Filename Structure

Example IMERG file:

3B-HHR.MS.MRG.3IMERG.20240501-S000000-E002959.0000.V07B.HDF5


Meaning:

20240501 → date

S000000 → start time (00:00)

E002959 → end time (00:29)

V07B → version

The dataset page provides clear metadata, variables, and documentation, but programmatic access requires a login workflow.

4. Data Access Method (Authentication Required)

Accessing IMERG via GES DISC is not anonymous.
The dataset requires:

- NASA Earthdata Login account
- Approval of the “NASA GESDISC DATA ARCHIVE” application
- Programmatic access via NASA EDL Application Token
Authentication Attempts

Several methods were attempted:

Method	Result
Username + password	        --> 401 Unauthorized
.netrc machine-based auth	--> 401 Unauthorized
Cookies / redirect session	--> 401 Unauthorized
EDL Application Token	    --> Successful

This confirms that for IMERG Half-Hourly (V07B), NASA enforces token-based security.

Final Working Method

The download script uses:

Authorization: Bearer <token>


in the HTTP header via a Python requests.Session().

This method successfully downloads files after the token is generated under:

Earthdata Profile → Applications → Create Application.

5. Download Procedure Implemented

The notebook follows these steps:

Define the date range: 2024-05-01 to 2024-05-03

Convert each date into day-of-year (DOY)

Construct the correct IMERG file URL

Add NASA EDL token for authentication

Download one IMERG Half-Hourly file per day

Save files into the folder: imerg_data/

Example successful output:

Processing 2024-05-01
Downloaded successfully!

Processing 2024-05-02
Downloaded successfully!

Processing 2024-05-03
Downloaded successfully!


These files appear in the output folder with sizes of ~5–10 MB each.

6. Problems Encountered & Solutions
✔ Authentication Issues

The main challenge was multiple 401 Unauthorized errors using traditional login methods.

Solutions attempted:

.netrc → Failed

Cookies + redirect → Failed

BasicAuth → Failed

Correct file paths verified → Still failed

DAAC approval verified → Still failed

Final Solution:
Generate an EDL Application Token and use it as a Bearer token in HTTP headers.

✔ Complex directory structure

The DOY (day-of-year) indexing required converting calendar dates to DOY.

✔ File naming

IMERG filenames are long; ensuring the correct timestamps was important.

Once authentication and naming were correct, downloading worked smoothly.

7. Scalability and Future Improvements

If the script needs to scale (e.g., download a full month or year of IMERG):

Performance Enhancements

Parallel downloads (multiprocessing)

Chunk-based file downloading

Using the AWS Open Data mirror (faster for large requests)

Code Improvements

Retry logic for failed downloads

Automatic token refresh if it expires

Logging instead of print statements

User-configurable date ranges and product versions

Storage Considerations

IMERG produces 48 files per day.
A full month ≈ 1.5–3 GB of data.
Users should plan storage accordingly.

8. References

NASA GES DISC IMERG Dataset
https://disc.gsfc.nasa.gov/datasets/GPM_3IMERGHH_07/summary

IMERG Algorithm Documentation
https://gpm.nasa.gov/resources/documents/imerg-atbd

Earthdata Login
https://urs.earthdata.nasa.gov/