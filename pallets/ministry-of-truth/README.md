# Ministry of Truth #

### A pallet focused on interpretability and consensus of claims made in the scientific community ###

#### What is this? ####
This pallet is inspired by the paper titled [*The use of distributed consensus algorithms to curtail the spread of medical misinformation*](https://www.ijam-web.org/article.asp?issn=2455-5568;year=2019;volume=5;issue=2;spage=93;epage=99;aulast=Plaza). The paper lays out a hypothetical case for a private blockchain solution to reduce groupthink, improve the consenssus-gathering of the scientific process, as well as improve the interpretability of the scientific results. The authors of the paper give several examples of cases where such a system may have countered the spread of medical misinformation. One example is the anti-vaccination movement in the 90's following the publication of a paper which was widely regarded as faulty by the scientific community.

#### What does the name mean? ####
The name of the pallet is inspired by the [propaganda arm of the government in *1984*](https://en.wikipedia.org/wiki/Ministries_of_Nineteen_Eighty-Four#Ministry_of_Truth), that serves to determine what information is true. It's just a silly reference and has nothing to do with the function of the pallet

#### How would it work? ####
The pallet should follow the author's guidance regarding behavior and voting. It should facilitate steps in the scientific process. In this case, the step to facilitate is a post-publish peer review step. The author recommends in the paper that a private blockchain consisting of anonymized node operators who are members of the scientific community be implemented. Ostensibly, this is to reduce groupthink in voting. A similar reduction in bias might be achieved if the pallet is split into multiple voting steps, and different instances of `Collective` consisting of randomized participants are used in voting. 

 In this case, the step followed will be a post-publish peer-review. In this stage of development, it should be used for papers pre or post-publish that are accesible through a URL. The usage of the system can be described through the following flow:

1. The user enters the app UI(imagine in this case we have built a UI form specifically for this pallet).
2. The user enters their article url into the form and submits it.
3. The UI sends a request to the node, requesting that the pallet store the article using the `propose_article` extrinsic.
4. This stores the article in a StorageMap, `ArticleStorage`, which is designated for content that is in the pre-review process of Ministry of Truth. The `claims` vec of this struct is initialized as empty.
5. The content will now be shown in the UI, under a pre-review page, along with any other pre-review content in the same `StorageMap`.
6. Others in the scientific community, that are validated members in the `council` instance of the *Collective* pallet can cast votes on these articles.
7. These members can now participate in the claims-voting step in the process. They can identify an objective claim statement for a claim made by the article, and put it to vote by using the Claims UI. This part of the UI contains a form that raises a motion in the *Collective* pallet that proposes calling the Ministry of Truth's *store_claim* extrinsic with: their objective claim statement, and the article ID the claim was discovered in.
8. Other members can vote aye/nay on such claims. Aye = credible claim. Nay = credible claim, or not objective statement. In the future, claims will further be split into voting steps of: 1. determining whether claims are objective, and b. determining whether claims are true. This can be split between different instances of collective, with randomized members.

## Interacting with the Pallet ##
1. Build the node `cargo build --release`
2. Run the dev chain locally `./target/release/node-template --dev --tmp`
3. Interact with the node via the polkadotjs UI:
	1. Use the `store_article` extrinsic
		1. Go to https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/explorer
		2. Choose the `storeArticle` extrinsic of the `ministryOfTruth` pallet.(In real-world use, the `Collective` pallet `propose` extrinsic would call this extrinsic)
		3. Provide hex values for the article url and source id (DOI) for the article
		4. Submit the transaction
	2. Use the `store_claim_for_article` extrinsic
		1. Choose the `storeClaimForArticle` extrinsic of the `ministryOfTruth` pallet.(In real-world use, users would see proposed articles and would suggest and suggest claims for the article through the `propose` extrinsic of the `Collective` pallet)
		2. Provide hex values for the claim statement(objective text of the claim that the user has identified), article id(refers to an article id you got from the previous step), and `isRejected`(whether the claim is credible, or non-credible.)
		3. Submit the transaction
	3. In a real-world case, this is where users would gather and vote on the motions of whether to store the claims as "accepted" objective claims.

#### Fixes/Improvements/TODO ####
1. Add extrinsics that call the `Collective` `propose` extrinsic with the `Call` of the expected article/claim contents. This would make the experience more guided and rely less on the frontend code to provide an exact call to the Collective extrinsics. 
2. Fix issue where can't retrieve `claimsToArticles` from polkadotjs UI(It works in tests, but getting a strange error in the apps UI)
3. Fix BadOrigin error when using `ministry-of-truth` extrinsics from the `Collective` `propose` extrinsic.
4. Split claims extrinsic into two: one for voting on whether such claim was made in an article, and another for voting on claim veracity of verified objective claims. It would likely reduce bias if multiple different groups could vote on a. The claims made in the article, and b. whether those claims are accepted/rejected.
5. Add additional collective instances. Add code to randmize members of collective instances to ensure roles are rotated. 
6. Find way to implement article uuid identifiers. E.G.
	/// Article source identifier 
	// enum SourceId {
	// 	ISBN,
	// 	DOI,
	// 	ISSN,
	// 	PMID,
	// 	SICI
	// }
6.Handle unused result in store_claim_for_article extrinsic