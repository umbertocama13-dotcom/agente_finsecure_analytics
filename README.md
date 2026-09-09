# Financial Risk & Audit AI Agent
## Overview

A conversational AI agent that supports financial analysts in risk management and internal audit, combining LLM-based reasoning with financial data analysis.

## The Brief

FinSecure Analytics, a financial advisory and risk management firm, needs to continuously analyze large volumes of financial data to assess risk exposure, market trends, and regulatory compliance — a process that is time-consuming and prone to human error or oversight.

Goal: build an AI agent (LangChain + LlamaIndex, LLM-based) that:

Automatically analyzes financial reports — extracting KPIs and flagging anomalies
Simulates risk scenarios based on historical data and economic variables
Acts as a conversational audit assistant, answering questions about the analysis and suggesting corrective actions
Surfaces findings through a real-time visualization dashboard

Data for the exercise could be synthetic (LLM-generated mock financial documents).

## What I Built

I implemented the full pipeline requested, with particular emphasis on making the agent as deterministic and controllable as possible, given the audit/compliance context:

**Ingestion & preprocessing**: loading of .txt, .pdf, and .docx documents, text normalization, and chunking (RecursiveCharacterTextSplitter) with enriched metadata for traceability (source, section, category).
**Indexing & retrieval**: a vector index built with LlamaIndex (persisted to disk), exposed to the agent through a dedicated semantic retrieval tool.  

**Automated pre-agent analysis**: before the conversational agent is even activated, a deterministic workflow autonomously extracts KPIs (based on a canonical dictionary with aliases, expected units, categories) and risk metrics (VaR, duration, FX exposure, etc.), and flags anomalies by cross-checking values both between different documents and within the same document.

**Scenario simulation**: a module that generates future risk scenarios (probability, drivers, assumptions, short/long-term effects), both automatically on the extracted data and on-demand via a dedicated tool exposed to the agent.  

**Conversational agent**: built with LangChain (create_openai_tools_agent + AgentExecutor), temperature=0.0, a restrictive, compliance-oriented system prompt, with access to the pre-computed KPIs/risks/anomalies/scenarios (passed in as JSON) and about a dozen financial calculation tools (margins, ROI, net cash flow, threshold deviation, stressed values, etc.). Conversation memory uses a buffer with automatic LLM-based summarization once a token threshold is exceeded, avoiding any long-term memory retention.  

**Dashboard**: visualized in Colab with ipywidgets and matplotlib (KPI/risk/anomaly counts, critical risks, most likely scenario), plus an integrated text chat with the agent.  

**Validation**: a synthetic dataset with 5 deliberately inserted errors (3 cross-document, 2 intra-document) to test anomaly-detection capability.  

**Observed results**: the agent correctly filters out-of-scope requests and calls tools when needed (though it often requires an explicit prompt to trigger a tool call). KPI/risk values passed via JSON were manually verified against bilancio_consolidato_FY2025.txt and were found consistent.  

**Limitations & what I'd improve**: extracting KPIs/risks/anomalies from unstructured documents remains the weakest point — without a standardized input format it's hard to make extraction deterministic via code rather than via LLM. Anomaly detection in particular has an unresolved trade-off between false negatives (thresholds too strict) and false positives/duplicates (thresholds too permissive). With more time, I would standardize the input document format to shift more logic onto deterministic rules.

## Tech Stack

Python · LangChain · LlamaIndex · LLMs
