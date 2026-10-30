\# Offline Privacy Test Notes



\## Test Information

Date: 2026-10-30

Model: soc-analyst

Base Model: Llama 3.1 8B

Quantization: Q4\_K\_M

Context Size: 8192

Temperature: 0.2

Seed: 42

Ollama Version: 0.40.13



\## Test Objective

Verify that the local AI model can process SOC-related requests

without an active internet connection and observe its network activity.



\## Test Procedure

1\. Started the soc-analyst model using Ollama.

2\. Opened Windows Resource Monitor.

3\. Monitored Ollama processes under the Network tab.

4\. Disconnected the internet connection.

5\. Submitted a security-related question to the model.

6\. Observed the model response and network connections.



\## Observed Results

\- The model successfully responded while the internet was disconnected.

\- Resource Monitor showed Loopback connections only.

\- No external connections associated with Ollama were observed during the test.



\## Conclusion

The model successfully performed local inference without an internet connection.

No external network connections were observed during the monitoring period.



This test demonstrates offline operation during the tested session.

It does not guarantee the absence of all possible network activity outside the test period.



\## Test Status

Offline execution: PASS

External connections observed: NO

