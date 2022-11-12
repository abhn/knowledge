# Reviewing Addons

Link to the wiki guide: [wiki.mozilla.org](https://wiki.mozilla.org/Add-ons/Reviewers/Guide/Tools)

You need to be given admin permissions by an admin

Find the reviewer's dashboard here: `Tools > Editor tools`

Keep an eye on
1. Announcement - yellow bar up top, usually important
2. Queue summary - Green is recent addons (5 days), red are pending for a while (>10 days). We want most if not all green

### Queue
There are a couple of queues
-   Full reviews: new add-ons that have been submitted for review.
-   Pending updates: new versions of add-ons that have passed review before.
-   Moderated reviews: user comments (called _reviews_ on AMO, for maximum confusion) flagged for moderation.

### Review steps
- Reject
- Push to publish
Special cases
- Request more info
- Request super-review
- Comment

## Technical review

>Add-on reviewers help ensure add-ons are safe to use, reliable and clearly presented to users. We also provide quick, clear, and actionable feedback to developers if issues are found with their add-ons.

Steps
1.  **Automatic Review**: When an add-on is uploaded, it undergoes a number of automatic validation steps for the general safety of the add-on.
2.  **Content Review**: Within a fairly short time after submission, add-ons are inspected by a human to ensure that the listing adheres to content review guidelines. This includes metadata such as the add-on name and description.
3.  **Technical Code Review**: The source code of the add-on is inspected to ensure it is in compliance with our review policies.
4.  **Basic Functionality Testing**: Once the source code is verified safe, the add-on must be given a basic test in functionality to ensure that it acts as described.

Recommended to open review tools in a new profile with addons disabled

Focus on the addons with the highest weight. Number of factors contribute to the weight of an addon. The higher the weight, the more likely it is for that addon to be malicious / abusive

Step 1.
- Content review for hate / abusive language, spam or copyright infringement

Step 2.
- Technical code review -
	- Review history for previous messaging to the devs, notes for reviewers from previous reviewers
	- Go through each file one by one, starting with manifest.json
	- If the addon uses a third party library
		- Our system might detect it and grey
		- If our system doesn't recognize the library, look for the author provided info on the library and must be conforming to our [library usage guidelines](https://developer.mozilla.org/en-US/Add-ons/Third_Party_Library_Usage)
			- Check if the developer has provided a link to the exact file being used, as per our library usage guidelines.
			-   If the links are not available, please request more information using the reviewer reply feature.
			-   Verify the link is from the original maintainer’s website
			-   Verify the file included in the add-on is an exact match to the linked original file on the maintainer’s website.
			-   Search for the corresponding unminified file on the maintainer’s website
			-   Review the unminified file
### Tips and tricks
```
Reviewing add-ons is a lot about following data around through the security boundaries within the add-on. A web page has less privileges than a WebExtension content page, which has less privileges than the WebExtension background page.

We recommend that you concentrate on finding code where data is being injected or executed (e.g. use of innerHTML), then backtracking to see where the data originates to determine if it is safe.

Likewise, when data leaves the user’s computer, you’d want to backtrack to the origin to identify what exact data is being transmitted.

If you stop at each function that has a potentially malicious nature and then track forward to see where it is used, you will end up reviewing many files more than once. This approach takes substantially more time, and we do not recommend it.
```

Complete the addon review completely and send all feedback at once