# Python P2P File Sharing Network (BitTorrent Clone)

A lightweight, decentralized Peer-to-Peer (P2P) file-sharing network inspired by the BitTorrent protocol. Built entirely in Python using Flask, this system features a centralized Tracker for peer discovery and load distribution, alongside hybrid Nodes that act concurrently as both clients (leechers) and servers (seeders).

## 🚀 Key Features

* **Hybrid P2P Nodes:** Each node runs a multithreaded architecture. A Flask server runs in the background to serve file segments to other peers, while a CLI client thread handles user input and downloads.
* **Intelligent Tracker:** The central tracker monitors network state and active peers. It implements a custom load-balancing algorithm to dynamically distribute download ranges among available seeders, preventing network bottlenecks.
* **File Segmentation & Reconstruction:** Files are automatically divided into 10KB chunks (`.part` files) before distribution. The client downloads these segments concurrently from multiple peers and reconstructs the original binary file ensuring data integrity.
* **Fault-Tolerant Resuming:** The tracker maintains a state of `pendingRequests`, allowing nodes to resume pending downloads dynamically if interrupted.
* **Availability Thresholds:** The system ensures that peers are only selected as seeders if they possess at least 20% of the requested file segments, ensuring efficient bandwidth utilization.

## 🧠 System Architecture

The project consists of two main components communicating via RESTful HTTP requests:

1.  **`Tracker.py` (The Indexer)**
    * Maintains an active registry of all connected nodes (`peers`) and the specific file segments they hold.
    * Calculates the optimal distribution of file segments when a download is requested, splitting the workload evenly across multiple seeders based on their availability.
2.  **`Node.py` (The Peer/Client)**
    * **Server Thread:** Runs a Flask app on port `5001` exposing a `/downloadFile` endpoint to send local file chunks to other peers.
    * **Client Thread:** A command-line interface that allows users to join the network, register local files, request file lists from the tracker, and orchestrate the downloading and merging of file segments.

## 🛠️ Technologies Used
* **Language:** Python 3
* **Web Framework:** Flask (REST APIs)
* **Concurrency:** `threading` module for parallel client/server execution.
* **Networking:** `requests` library for peer-to-peer and peer-to-tracker HTTP communication.

## ⚙️ How it Works
1.  A Node requests a file from the Tracker.
2.  The Tracker identifies all active peers holding >= 20% of the file segments.
3.  The Tracker calculates segment ranges and assigns specific chunk intervals to different peers to balance the load.
4.  The requesting Node directly contacts the assigned peers (`Node to Node`) to download the `.part` files.
5.  Upon receiving all chunks, the Node automatically sorts and merges them back into the original file format.
