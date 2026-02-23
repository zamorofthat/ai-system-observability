# AI System Observability Pack

The AI System Observability Pack enables you to monitor and optimize your AI deployments using your existing observability infrastructure. Track costs, measure quality, and improve user experience across all your AI conversations.

This Pack enriches AI conversation logs with real-time analytics: cost tracking across 16+ models, quality grading (A-D), sentiment analysis, intent detection, and optimization recommendations. All processing happens in-stream with zero external API calls and zero latency impact.

## About this Pack

Here are some of the benefits this Pack provides:

* **Cost Intelligence** - Track per-conversation costs across OpenAI, Anthropic, Llama, and Mixtral models with automatic hourly/daily/monthly spend projections
* **Quality Monitoring** - Grade every response (A-D) based on relevance, personalization, actionable guidance, and empathy to catch problems before they become support tickets
* **User Experience Analytics** - Detect sentiment (positive/negative/neutral), classify intent (support, sales, technical), and identify urgency across 8+ languages
* **Optimization Recommendations** - Get actionable suggestions for model downgrades, caching opportunities, response length optimization, and streaming enablement
* **Real-Time Alerting** - Route high-cost, low-quality, or negative sentiment conversations to review queues or alert systems automatically
* **Zero Dependencies** - Uses pattern matching and statistical analysis with no external API calls, processing 10K+ conversations per second

## Architecture

This Pack uses a modular pipeline architecture that normalizes different AI log formats into a common schema, then enriches them with analytics.

```
┌──────────────────────────────────────────────────────────────────────────┐
│                           NORMALIZERS                                    │
│            (transform source-specific → common schema)                   │
│                                                                          │
│  ┌─────────────────────────┐  ┌─────────────────────────┐               │
│  │ claude_code_otel        │  │ (add your own)          │               │
│  │ _normalizer             │  │ e.g. gemini_normalizer  │               │
│  │         ↓               │  │      openai_normalizer  │               │
│  │ ai_normalization        │  │                         │               │
│  │ _pipeline               │  │                         │               │
│  └───────────┬─────────────┘  └────────────┬────────────┘               │
│              │                             │                             │
└──────────────┼─────────────────────────────┼─────────────────────────────┘
               │                             │
               ▼                             ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                      ai_enrichment_functions                             │
│                                                                          │
│   Expects: user, assistant, model fields                                 │
│   Outputs: cost analysis, quality grade, sentiment, intent, alerts       │
│                                                                          │
│   * Handles Claude Code (after normalization)                            │
│   * Handles Ollama, OpenAI, etc. (directly if already in schema)         │
└──────────────────────────────────────────────────────────────────────────┘
               │
               ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                      model_metrics_parse (optional)                      │
│                                                                          │
│   Aggregates enriched logs into metrics for dashboards                   │
└──────────────────────────────────────────────────────────────────────────┘
```

### Extending for New AI Sources

To add support for a new AI log type:

1. **If logs already have `user`, `assistant`, `model` fields** → Route directly to `ai_enrichment_functions`
2. **If logs need transformation** → Either:
   - Add parsing logic to `ai_normalization_pipeline`, or
   - Create a new normalizer pipeline + route

### Alternative: Standalone Pipeline

`ai_chat_enrichment_code` is a standalone all-in-one pipeline that combines parsing and enrichment for simpler deployments.

## Deployment

After installing this Pack, configure routing based on your AI log sources:

### Prerequisites
* Cribl Stream 4.0 or later
* AI conversation logs in JSON format flowing through your pipeline

### Configuration Steps

1. **For Claude Code OTEL logs:**
   - Logs are automatically routed via `claude_code_otel_normalizer` → `ai_normalization_pipeline` → `ai_enrichment_functions`
   - No additional configuration needed

2. **For other AI logs (Ollama, OpenAI, etc.):**
   - If logs have `user`, `assistant`, `model` fields, route directly to `ai_enrichment_functions`
   - If logs need transformation, add parsing to `ai_normalization_pipeline` or create a new normalizer

3. **Update model pricing (optional):**
   - Edit `data/lookups/model_costs.csv` to add or update model pricing
   - The lookup is used for cost calculations in `ai_enrichment_functions`

4. **Configure outputs:**
   - The pipeline adds 15+ enriched fields to each event (see Output Fields below)
   - Route enriched events based on your needs:
     - High-quality conversations (Grade A/B) → Primary analytics destination
     - Low-quality conversations (Grade C/D) → Quality review queue
     - High-cost conversations → Cost monitoring dashboard
     - Negative sentiment → Priority alerts

5. **Customize thresholds (optional):**
   - Edit `ai_enrichment_functions` to adjust:
     - Quality grade thresholds (default: A≥80, B≥60, C≥40)
     - Cost alert threshold (default: $0.05)
     - Cache recommendation criteria

### Output Fields

Key enriched fields added by this Pack:
* `_conversation_summary` - One-line summary with all key metrics
* `_cost_analysis.*` - Cost breakdown and projections
* `_response_quality.*` - Quality score and grade
* `_conversation_intelligence.*` - Intent, language, keywords
* `_sentiment_analysis.*` - Sentiment, urgency, confusion detection
* `_optimization_opportunities[]` - Array of recommendations
* `_alerts[]` - Array of triggered alerts

See full field documentation in the function comments.

## Upgrades

Upgrading certain Cribl Packs using the same Pack ID can have unintended consequences. See [Upgrading an Existing Pack](https://docs.cribl.io/stream/packs-tutorial/#upgrading-an-existing-pack) for details.

**Important:** If you've customized `data/lookups/model_costs.csv` with custom model pricing, back up your changes before upgrading.

**Upgrade process:**
1. Export your current Pack configuration (optional backup)
2. Back up `data/lookups/model_costs.csv` if customized
3. Install the new Pack version
4. Restore custom model pricing to the lookup file
5. Test with sample data before routing production traffic

## Release Notes

### Version 1.0.0 - 2025-02-22
Initial release
* Real-time cost tracking across 16+ AI models (OpenAI, Anthropic, Llama, Mixtral)
* Quality grading (A-D) based on 6 scoring factors
* Sentiment analysis with urgency and confusion detection
* Intent classification (support, sales, technical, billing, etc.)
* Multi-language detection (English, Spanish, French, German, Portuguese, Chinese, Japanese, Korean, Arabic)
* Optimization recommendations (model downgrades, caching, streaming)
* Real-time alerting for high-cost, negative sentiment, and low-quality conversations
* Token efficiency analysis and waste detection
* ROI calculation per conversation


## Contributing to the Pack

To contribute to this Pack, report issues, or request enhancements, please:
* **GitHub:** Submit issues or PRs at [https://github.com/zamorofthat/ai-system-observability]
* **Cribl Community Slack:** Connect with Aaron Zamora in the #packs or #integrations channels

We welcome contributions including:
* Additional model pricing data
* New optimization recommendation patterns
* Quality scoring improvements
* Dashboard/visualization examples
* Bug fixes and performance improvements

## Contact

To contact us please join the [Cribl Community Slack](https://cribl.io/community/) and find us in the #packs channel.

## License

This Pack uses the following license: [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).

