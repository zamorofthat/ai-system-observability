# AI Observability Pack

The AI Observability Pack enables you to monitor and optimize your AI deployments using your existing observability infrastructure. Track costs, measure quality, and improve user experience across all your AI conversations.

This Pack enriches AI conversation logs with real-time analytics: cost tracking across 16+ models, quality grading (A-D), sentiment analysis, intent detection, and optimization recommendations. All processing happens in-stream with zero external API calls and zero latency impact.

## About this Pack

Here are some of the benefits this Pack provides:

* **Cost Intelligence** - Track per-conversation costs across OpenAI, Anthropic, Llama, and Mixtral models with automatic hourly/daily/monthly spend projections
* **Quality Monitoring** - Grade every response (A-D) based on relevance, personalization, actionable guidance, and empathy to catch problems before they become support tickets
* **User Experience Analytics** - Detect sentiment (positive/negative/neutral), classify intent (support, sales, technical), and identify urgency across 8+ languages
* **Optimization Recommendations** - Get actionable suggestions for model downgrades, caching opportunities, response length optimization, and streaming enablement
* **Real-Time Alerting** - Route high-cost, low-quality, or negative sentiment conversations to review queues or alert systems automatically
* **Zero Dependencies** - Uses pattern matching and statistical analysis with no external API calls, processing 10K+ conversations per second

## Deployment

After installing this Pack, configure the AI Analytics Pipeline:

### Prerequisites
* Cribl Stream 4.0 or later
* AI conversation logs in JSON format flowing through your pipeline
* Logs must include fields: `model`, `user.message`, `assistant.message`

### Configuration Steps

1. **Route your AI logs to the pipeline:**
   - Navigate to Routes and create a new route
   - Set filter to match your AI conversation logs (e.g., `dataSource=='ai_conversations'`)
   - Set output to `AI Analytics Pipeline`

2. **Review the AI Analytics Function:**
   - Open `Pipelines > AI Analytics Pipeline`
   - Review the `ai_conversation_analytics` function
   - Update `MODEL_COSTS` object if you use models not listed (defaults to 16 common models)

3. **Configure outputs:**
   - The function adds 15+ enriched fields to each event (see documentation)
   - Route enriched events based on your needs:
     - High-quality conversations (Grade A/B) → Primary analytics destination
     - Low-quality conversations (Grade C/D) → Quality review queue
     - High-cost conversations → Cost monitoring dashboard
     - Negative sentiment → Priority alerts

4. **Optional: Customize thresholds:**
   - Edit the function to adjust:
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

**Important:** The `ai_conversation_analytics` function contains the `MODEL_COSTS` configuration object. If you've customized model pricing, back up your changes before upgrading. After upgrade, re-apply your custom pricing.

**Upgrade process:**
1. Export your current Pack configuration (optional backup)
2. Note any custom `MODEL_COSTS` values
3. Install the new Pack version
4. Re-apply customizations to `MODEL_COSTS` if needed
5. Test with sample data before routing production traffic

## Release Notes

### Version 1.0.0 - 2025-01-XX
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

To contact us please email azamora@cribl.io or find me on Cribl Community Slack (@aaron).

## License

This Pack uses the following license: `Apache 2.0`.

