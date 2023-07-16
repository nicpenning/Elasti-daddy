# Blog Post #10.
## Final Words

💫 Wow, what an adventure it was to build an entire integration from the ground up with our own custom data source.

I learned a lot about what it takes to build, tweak, tune, and manage integrations throughout this entire blog
post series. It was quite the challenge to walk through all of the steps to get through this series. Just to get 
started by having the right environment takes a lot of work and I have not yet found a consolidated resource that
puts together all of the requirements as well as this series does. Even after having the integration, I was able to
find a bug or request an enhancement here and there that I could report back to their respective projects:

- [[ML] Update wording during upload file wizard with "Next"](https://github.com/elastic/kibana/issues/159826)
- [[ML] Kibana Upload - Improve import process on failed import by handling indices that already exis](https://github.com/elastic/kibana/issues/159829)
- [[Integrations] Ingest Pipelines not showing under Integration Assets tab](https://github.com/elastic/kibana/issues/160555)
- [Check package name creation during wizard and/or improve error logging for manifest.yml](https://github.com/elastic/elastic-package/issues/1346)
- [Support for Docker Compose V2 + Compose V1 Nearing End of Life](https://github.com/elastic/elastic-package/issues/1306)
- [Elastic Stack Up not working on Windows or Ubuntu WSL](https://github.com/elastic/elastic-package/issues/1282)

There are still a few topics on Elastic integrations that were not explored that perhaps could make
for great bonus blog posts down the road. One of the topics is advanced testing where you can supply raw events
and expected events to test against. This would ensure that any changes do not break the integration. Another
topic is the process of updating the changelog on every new modification, whether it was a bug fix or enhancement. This 
will coincide with creating pull requests to the official `Elastic Integrations` repository where they can be enjoyed by
all users of Elastic.

All in all, this will be a good reference for myself and any others that need some guidance on how to tackle any of these specific
tasks throughout the series.

🙏 I hope others find inspiration in creating their own integrations or even simply making current integrations better for all.

I will leave you with these final words:

```
All your children shall be taught by the Lord,
And great shall be the peace of your children

                              -Isiah 54:13
```

*May the Lord bless you and keep you.*

*May the Lord make his face to shine upon you, and be gracious to you.*

*May the Lord lift up his countenance upon you, and give you peace.*

Thank you!
