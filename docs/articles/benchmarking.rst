*****************
Benchmarking
*****************

Periodically we will be publishing results of benchmarking the Synapse Python Client
compared to directly working with AWS S3. The purpose of these benchmarks is to make
data driven decisions on where to spend time optimizing the client. Additionally, it will
give us a way to measure the impact of changes to the client.

===================
Results
===================


12/06/2023: Uploading files to Synapse, Varying thread count, 5 annotations per file
====================================================================================
The results were created on a `t3a.micro` EC2 instance with a 200GB disk size running in us-east-1.
The script that was run can be found in `docs/scripts`. The time to create the files on disk is not included.

This test includes adding 5 annotations to each file, a Text, Integer, Floating Point, Boolean, and Date.

S3 was not benchmarked again.

As a result of these tests the sweet spot for thread count is around 50 threads. It is not reccomended to
go over 50 threads as it resulted in signficant instability in the client.

+---------------------------+--------------+-------------------+---------------------+---------------+
| Test                      | Thread Count | Synapseutils Sync | os.walk + syn.store | Per file size |
+===========================+==============+===================+=====================+===============+
| 25 Files 1MB total size   | 6            | 10.75s            | 10.96s              | 40KB          |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 25 Files 1MB total size   | 25           | 6.79s             | 11.31s              | 40KB          |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 25 Files 1MB total size   | 50           | 6.05s             | 10.90s              | 40KB          |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 25 Files 1MB total size   | 100          | 6.14s             | 10.89s              | 40KB          |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 775 Files 10MB total size | 6            | 268.33s           | 298.12s             | 12.9KB        |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 775 Files 10MB total size | 25           | 162.63s           | 305.93s             | 12.9KB        |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 775 Files 10MB total size | 50           | 86.46s            | 304.40s             | 12.9KB        |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 775 Files 10MB total size | 100          | 85.55s            | 304.71s             | 12.9KB        |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 10 Files 1GB total size   | 6            | 27.17s            | 36.25s              | 100MB         |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 10 Files 1GB total size   | 25           | 22.26s            | 12.77s              | 100MB         |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 10 Files 1GB total size   | 50           | 22.24s            | 12.26s              | 100MB         |
+---------------------------+--------------+-------------------+---------------------+---------------+
| 10 Files 1GB total size   | 100          | Wouldn't complete | Wouldn't complete   | 100MB         |
+---------------------------+--------------+-------------------+---------------------+---------------+


11/14/2023: Uploading files to Synapse, Default thread count
============================================================
The results were created on a `t3a.micro` EC2 instance with a 200GB disk size running in us-east-1.
The script that was run can be found in `docs/scripts`. The time to create the files on disk is not included.

This test uses the default number of threads in the client: `multiprocessing.cpu_count() + 4`

+---------------------------+-------------------+---------------------+---------+---------------+
| Test                      | Synapseutils Sync | os.walk + syn.store | S3 Sync | Per file size |
+===========================+===================+=====================+=========+===============+
| 25 Files 1MB total size   | 10.43s            | 8.99s               | 1.83s   | 40KB          |
+---------------------------+-------------------+---------------------+---------+---------------+
| 775 Files 10MB total size | 243.57s           | 257.27s             | 7.64s   | 12.9KB        |
+---------------------------+-------------------+---------------------+---------+---------------+
| 10 Files 1GB total size   | 27.18s            | 33.73s              | 16.31s  | 100MB         |
+---------------------------+-------------------+---------------------+---------+---------------+
| 10 Files 100GB total size | 3211s             | 3047s               | 3245s   | 10GB          |
+---------------------------+-------------------+---------------------+---------+---------------+
