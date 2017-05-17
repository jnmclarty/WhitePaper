<h3 align='center'>DHARMA.IO</h3>
<h4 align='center'>A DECENTRALIZED PROTOCOL <br>FOR PEER-TO-PEER LENDING</h4>
<p align='center'>v1.0</p>

<p align='center'>Nadav Hollander</br>
<a href='mailto:nadav@dharma.io'>nadav@dharma.io</a></br>
<i>B.S. in Computer Science</br>
Stanford University ‘17</i></br>
</p>

<h5 align='center'>ABSTRACT</h5>

  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;In this white paper, we present a novel protocol for decentralized issuance, origination, risk-assessment, and underwriting of peer-to-peer loans on blockchains supporting requisite smart contract functionality (most notably, the Ethereum blockchain).  We leverage a tiered architecture, in which loans are issued as smart contracts adhering to a standardized interface a la ERC20, borrowers and lenders are matched via decentralized origination software, and fraud, default, and compliance risk are mitigated via centralized risk-assessment attestors.  The protocol is designed to support a robust ecosystem of competing software clients, risk-assessment mechanisms, and last-mile providers to bring loan products of flexible ticket-size and type to any credit-worthy individual in the world in a seamless, digital manner.

<h5 align='center'>INTRODUCTION</h5>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Cryptocurrencies and blockchains hold tremendous potential for delivering financial services to the estimated 2 billion adults who lack access to them today due to political, social, or economic factors.   Digital currencies and blockchain technology hold promise to disintermediate the monopolistic stalwarts of the financial industry and give financial services the same affordability, global accessibility, developer extensibility, and ease-of-use the Web gave information and content.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;In this spirit, several attempts have been made at leveraging the borderless nature of cryptocurrencies in order to provide online lending services in a similarly borderless fashion through peer-to-peer lending platforms<sup><a id="a1" href='#f1'>1</a></sup>, to varying degrees of success.  In nearly all cases, however, regulatory barriers have proven to be a significant barrier to global-scale adoption.  This problem is not specific to crypto-currency based lending systems, but rather an intrinsic point of friction in the growth of the peer-to-peer lending industry -- operating a regulated
lending platform as globally accessible and borderless as, say, Facebook would demand compliance with a rat’s nest of state regulatory regimes, bilateral trade agreements, and international lending laws.

 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Thus, we propose a system in which the underlying issuance, pricing, payment, credit scoring, and origination mechanisms of the peer-to-peer lending process are abstracted away into a shared, decentralized infrastructure, enabling online borrowers to connect and transact with lenders without having to rely on heavily regulated marketplace lending platforms as intermediaries -- irrespective of a borrower’s location or access to traditional banking.   This as well as other impetus for the Dharma Protocol are elaborated on elsewhere.


<h5 align='center'>GOALS</h5>

Goal: _Build a globally accessible digital currency lending network in which_
1. Any individual with Internet access can quickly and practically take out a loan
2. Any individual with Internet access can reliably assess a borrower’s credit-worthiness and invest in loans anywhere in the world under a predictable risk-profile
3. Any enterprising entrepreneur can, with relatively little technical / regulatory overhead, tap into the Dharma network in order to add a line of credit into whatever application she envisions

<h5 align='center'>ARCHITECTURE</h5>

Inspired by the “thin-waisted” architecture of the Internet networking stack, we have designed the Dharma Protocol in such a manner that interoperability and modularity are intrinsic.  The protocol is abstracted into three layers: the “Loan Layer”, the “Origination Layer”, and the “Risk-Assessment Layer”.  Much like the layers of the Internet networking stack, each layer allows for a host of competing implementations and clients.

1. **Loan Layer**: Serves as a vehicle for loan issuance, payment, storage, and transfer.
2. **Origination Layer**: Matches borrowers to lenders and mediates loan pricing in a decentralized manner.
3. **Risk-Assessment Layer**: Assesses and mitigates a borrower’s credit default risk.

<p align='center'><img src='images/network_architecture.png'></img></p>

_NOTE: None of the entities in the figure above have provided any sort of explicit  endorsement of the Dharma Protocol and are simply used as hypothetical explanatory examples_

_NOTE: While the Dharma Protocol is designed to support a gamut of blockchains at its lowest level, this white paper outlines the function of each layer within only the context of Solidity smart contracts on top of Ethereum.  Future work will detail the Dharma Loan Standard abstraction in a broader, more generally applicable description in order to allow for modularity at the blockchain level._

<h6 align='center'>LOAN LAYER</h6>

Every loan in the Dharma protocol is represented by an individual smart contract that serves as a central hub for all business logic related to loan payouts, ownership, attestation data, and term data.  Contracts must strictly adhere to the Dharma Loan Standard in order to be considered valid loans.  **The Dharma Loan Standard is a super-set of the ERC20 token standard, in that investors’ stakes in loans can be traded and stored as digital tokens in ERC20-compliant exchanges and wallets.**

The Dharma Loan Standard (DLS) requires a loan contract implement the following functionality:

1.  `fundLoan()`:<br>
 **Serving as a vehicle for crowdfunding the loan**<br>_(i.e. investors Rick and Morty send X Ether to the contract, the contract transfers principal raised to borrower Jerry, contract records Rick and Morty’s ownership prorated by the amount each contributed)_

2. `periodicRepayment()`:<br>
**Serving as a vehicle for all principal repayments and interest payments to investors + compensating the risk-assessment attestor a pre-defined portion of the loan principal**<br>
_(i.e. Jerry makes monthly payment + interest to the contract, contract distributes the repayment + interest to loan investors prorated by the amount each contributed, with a predefined portion allotted for the risk-assessment attestor)_

3. `getTermsValue(bytes32 key)`:<br>
**Storing and exposing the terms of the loan**<br>
_(i.e. Jerry’s loan has an interest rate of 10%, a term length of 2 months, a bi-monthly amortization schedule, and is not collateralized)_

4. `attestTo(byte32 attestationUrl) onlyAttestor / getAttestation()`:<br>
**Storing a link to a publicly auditable risk assessment attestation (explained in greater detail in the section titled “Risk-Assessment Layer”)**<br>
_(e.g. “Galactic Federation Inc. attests that they have verified Jerry is not
a robot and rate his credit risk profile at BB with 33% likelihood of default”)_

5. [The ERC20 Token Standard](https://github.com/ethereum/EIPs/issues/20):<br>
**Allowing transfer of ownership for investors**<br>
_(i.e. Morty sells his stake in the loan to Summer and transfers his stake over to her)_

A reference implementation for a simple loan prototype can be found [here](http://gateway.ipfs.io/ipfs/QmemEAsE3iWVewPHYnTvK4pCDnDKNXE1EKH8aXGf9DR2NL).

<h6 align='center'>ORIGINATION LAYER</h6>

The function of the origination layer is to match borrowers to lenders in a manner that is decentralized and censorship-resistant.  

From the borrower perspective, origination layer clients are responsible for:

1. Ensuring borrower success at all steps of a loan’s life-cycle, by:
    - Allowing borrowers to deploy DLS-adherent contracts from a set of loan-type templates.
    - Providing an interface through which borrowers can procure a Risk-Assessment Attestation
    - Reminding borrowers of impending repayment dates

From the lender perspective, origination layer clients are responsible for:

1. Allowing lenders to browse through currently unfunded DLS-adherent loan contracts
2. Allowing lenders to audit Risk-Assessment Attestation data associated with loan contracts
3. Allowing lenders to examine reputational metrics associated with any given Risk-Assessment Attestor
4. Allowing lenders to invest in and manage a portfolio of loans
5. Giving lenders a means of communicating with borrowers in both considering investments and throughout the fulfilled loan’s term.
6. Giving lenders a means of rating their communications with borrowers ex post facto

There are a number of ways to implement clients geared toward this end -- each with their own tradeoffs and bottlenecks.  The one detailed here is the Dharma Loan Browser, but we encourage developers to tackle competing implementations better optimized toward different loan use-cases and types.

_Dharma Loan Browser_<br>
The Dharma Loan Browser is an Electron application that allows investors to browse a portfolio of unfulfilled loans by crawling the Ethereum blockchain in search of contracts adhering to the Dharma Loan Standard that are, as of yet, not fully funded.  In order to identify adherent contracts, the loan browser asserts equality between a given contract’s byte code and the compiled byte codes of a set of reference loan contract implementations.  This assertion is necessary in order to prevent malicious implementations of the DLS-adherent loans from appearing in the Loan Browser.  The Browser allows lenders to examine risk-assessment attestation data associated with each loan, audit standardized metrics that reflect a risk-assessment attestor’s reliability, filter and search through loans on a number of criteria, and, most importantly, serves as a wallet for funding and managing a portfolio of loan contracts.

<p align='center'><b>FAQ</b>: Who maintains the contract reference set?</p>

<h6 align='center'>RISK-ASSESSMENT LAYER</h6>

The amount of lending capital that will enter the Dharma Ecosystem is a direct function of the risks lenders are willing to take on by investing.  The purpose of the Risk-Assessment Layer is to mitigate the fraud, default, and compliance risks lenders are exposed to.  This is accomplished by requiring DLS-adherent loans to expose a publicly auditable, signed attestation from a Risk-Assessment Attestor.

_What is a Risk-Assessment Attestor (RAA)?_<br>
An RAA is a centralized entity that, for a pre-defined percentage of the loan principal, assesses the veracity of a borrower’s identity and credit-worthiness, and cryptographically signs an attestation containing an assessment of the borrower’s risk profile.  Moreover, RAAs provide value-added services to minimize lender compliance risk with respect to usury regulations and   restrictions on sanctioned individuals or entities.  **RAA’s are held accountable to their assessments by a standardized set of metrics that are computed off-chain by origination layer clients and presented to lenders as a measurement of RAA trustworthiness and competence.**

> Example: Galactic Federation Inc. is an RAA with a publicized public key of XYZ.
If Jerry wants to take out a loan on the Dharma network, he first goes to
Galactic Federation Inc’s website and begins going through their identity
verification flow.

_Assessing Fraud Risk_<br>
Identity verification and fraud detection are crucial to the success of Dharma insofar as, without a reliable identity system, a capable Sybil attacker would be able to default on as many loans as he pleases without adverse repercussions on his future credit-worthiness.  As such, the first, and arguably most important, function of the Risk-Assessment Layer is to assert the veracity of a borrower’s identity.  While promising decentralized identity projects are gaining traction within the blockchain community, we see the short-term route to addressing this risk vector as relying on trusted third parties for identity verification.  RAAs can utilize a variety of data points in order to assert user identity, including:

- Governmental Identification Documents
- Biometric Identification Systems
- Social Networking Profiles
- Proxy forms of identity (e.g. telephone numbers, social security numbers)
- Social Attestation Schemes (i.e. friends and family members vouch for a borrower)
- ML-based Fraud Detection Pipelines (e.g. Sift Science)

> Example: Jerry is prompted to upload a scan of his passport, connect his Facebook profile,
and undergo a quick phone screen with a representative of Galactic Federation Inc.

_Assessing Credit-Worthiness_<br>
Credit markets function most efficiently when lenders can make investments under relatively predictable risk-profiles.  RAAs perform credit-assessment due diligence using a combination of traditional and non-traditional data points including:

- Granular repayment history for loans taken out on the Dharma Network.
- FICO Scores
- Income Statements
- Mobile Data
- Web-of-Trust based schemes (see Future Work)

Moreover, RAAs are capable of reporting borrower defaults to relevant credit bureaus on the behalf of lenders.  **Thus, borrowers are disincentivized from defaulting on loans insofar as their future creditworthiness, both within the Dharma network and in traditional loan markets, will be adversely affected.**

> Example: Jerry has no loan history on the Dharma network, so Galactic
Federation Inc. pulls a FICO score on Jerry from TransUnion, and asks Jerry to
install a mobile app that feeds Jerry’s mobile usage data into a predictive
credit-scoring model.

_Mitigating Compliance Risk_<br>
By investing in loans, lenders are exposing themselves to a varied gamut of compliance requirements related to usury laws, reporting requirements, and taxation.  RAAs create an investment environment more palatable to sophisticated institutional investors by providing compliance services including:

- OFAC Screening
- AML / KYC Compliance
- Truth In Lending Act Disclosures

> Example: Galactic Federation Inc. verifies that Jerry is not a sanctioned
Individual and prices the loan in accordance to usury regulations in Jerry’s
home planet..

When a borrower’s identity has been sufficiently verified, the RAA signs a message containing all relevant data points produced in its risk-assessment processes using the private key associated with its publicized public key.  A borrower’s true identity remains obfuscated and the attestation simply provides the RAA’s assessment of the borrower’s likelihood of default.  Once the borrower deploys a loan contract to the network and the RAA has verified that the contract will indeed compensate the RAA the agreed upon percentage of the loan, the RAA will add its signed attestation to the loan contract.

**In order to bootstrap the Dharma Loan Network and iterate on the protocol, Dharma.io will provide RAA services as part of a developer alpha program.  As time goes on, we hope other RAAs will emerge in order to create an ecosystem of attestors with varying loan type and risk profile expertise, and plan on creating tools that give entities means of becoming RAAs with little technical / regulatory overhead.**

_Who would a Risk-Assessment Attestor be?_<br>
Any entity in the world with a reasonable proxy for assessing a borrower’s identity and an interest in delivering credit products is in a profitable position to become an RAA.  This could include:
1. Mobile Wallet Providers
2. Indian entrepreneurs leveraging the Aadhaar Biometric Identification System
3. African telcos leveraging phone usage data
4. NGOs and International Aid Initiatives
5. Microcredit Organizations
6. Existing Online Lenders
7. Religious Organizations

_RAA Accountability_:<br>
Initially, the number of entities providing RAA services will likely be small enough such that informal, social reputation will be sufficient to drive the market towards the best RAAs and deter malicious behavior.  As the network grows, however, a formalized reputation system will be critical to deterring RAAs from colluding with fraudulent borrowers.  We propose a simple $F_{\beta}$ metric for RAA accountability that borrows from the statistical analysis of binary classification in order to classify how accurate RAA default predictions are:

Let $x \in {1,...,n}$ be a loan in the RAA’s portfolio of the $n$ loans he has attested to<br>
Let $\alpha_x$ be the principal balance and and accrued interest the borrower of $x$ is liable for<br>
Let $\gamma_x$ be the amount paid back by the borrower in actuality<br>
Let $\delta_x$ be the probability of $x$ defaulting, as predicted in the RAA’s attestation.<br>
Let $\beta$ be a hyperparameter we use to weight the importance of recall vs. precision<br>
\[ p =\frac{\sum_x min(\alpha_x - \gamma_x, \delta_x\alpha_x)}{\sum_x \delta_x\alpha_x}\]
\[r=\frac{\sum_x min(\alpha_x - \gamma_x, \delta_x\alpha_x)}{\sum_x(\alpha_x - \gamma_x)}\]
\[ F_\beta=\frac{(1+\beta^2)pr}{\beta^2p + r} \]

The $F_\beta$  metric is a helpful signal as to an RAA’s ability to predict defaults, but, from a borrower perspective, does not provide a signal as to how much weight the RAA’s attestation carries with investors.  By dividing the sum value of loans funded by the sum value of loans requested in an RAA’s attested portfolio, we can compute a simple baseline metric with which borrowers can evaluate how likely it is that their loan will be funded with an RAA’s attestation.  Future work will go into integrating metrics and signals that more accurately account for inherent differences in the RAA’s locales and risk-profile specialties (i.e. an RAA that specializes in attesting to subprime borrowers would be unfairly judged by the last metric).

<h6 align='center'>LOAN LIFECYCLE</h6>

A typical loan goes through the following lifecycle:

1. **RISK ASSESSMENT PERIOD**<br>
A borrower begins the risk assessment process by either applying for an attestation through an RAA’s website, through the Dharma Loan Browser, or, ideally, through a white-label UI integrated into popular wallet provider application (e.g. Coinbase, M-PESA).    The process could be as short as 2 minutes or as long as 2 weeks -- different loan types and ticket sizes will demand different standards of security.

2. **TERM NEGOTIATION PERIOD**<br>
In the alpha release of the protocol, the RAA signs an attestation that contains an assessment of the borrower’s creditworthiness as well as suggested terms for the loan (i.e. interest rate, late payment penalties, repayment schedule).  It is in the borrower’s discretion whether they wish to deploy the loan contract with the suggested terms, though it is unlikely lenders will want to invest in a loan that deviates significantly from the suggested terms.  The only non-negotiable aspect of the contract is the percentage of the loan that will be paid out to the RAA.  In the beta and onwards, we will move towards more sophisticated, distributed means of term negotiations (e.g. Dutch Auctions for interest rates).

3. **LOAN FUNDING PERIOD**<br>
The borrower deploys the template loan contract with the RAA’s public key hard-coded as the loan’s designated attestor.  Once the RAA verifies that their agreed upon compensation percentage is hard-coded into the contract and that the contract is not maliciously implemented, the RAA attaches their attestation to the loan.  The contract includes the requested loan amount and a funding period timelock date -- if the loan is not fully funded and the timelock date lapses, investors who’ve already sent tokens to the loan contract will have the ability to withdraw their investment.  When the loan hits its funding target, the contract transfers the balance to the borrower.  Initially, the evaluation and funding of loans will be a manual, human process -- loans will likely be funded within a similar timetable as they are on peer-to-peer lending platforms today.  In the future, however, standardized machine readable attestation schemas will enable lenders to automate the investment process on the basis of a portfolio of desired risk parameters, reducing the loan funding period to the range of minutes.

4. **REPAYMENT PERIOD**<br>
Loan repayment will either be expected on a single date, on an amortized basis over a fixed repayment schedule, or on a revolving basis.  Reminding borrowers of impending payback dates through a variety of channels will be the responsibility of RAAs, since RAAs have a salient interest in minimizing the default rate of loans in their attestation portfolio

5. **COLLECTION PERIOD**<br>
If a borrower neglects to meet a repayment deadline and a predefined grace period has lapsed, the loan enters a state of default.  In the short-term, lenders will likely be limited in their means of making themselves whole again.  If the network achieves critical mass and smart contracts begin receiving commonplace legal recognition, however, entities similar to collection agencies could emerge, purchasing loans in default from lenders for a fraction of their nominal value and collecting on them using traditional levers of the legal system.

<h6 align='center'>CHALLENGES</h6>

_Default Deterrence_<br>
In traditional retail lending schemes, borrowers are dissuaded from defaulting for fear of primarily two things: repercussions on their future creditworthiness, and legal actions forcing either a forfeiture of assets or bankruptcy.  The Dharma Protocol replicates the first as part of the Risk-Assessment Attestation mechanism described above.  However, the levers of the legal system will likely be unavailable to lenders in the Dharma ecosystem, given the challenges inherent to employing legal resources in the context of international lending and, especially, developing world lending.    

_Identity Fraud_<br>
Lending money to strangers on the Internet via centralized peer-to-peer lending platforms is a risky enough endeavor as is -- in 2014, fraud was responsible for 12% of losses in the online lending industry -- assessing identity and fraud risk in a decentralized context will prove at least as challenging.  We plan to address fraud and Sybil attack vectors via the Risk-Assessment Attestation mechanism.

_Currency Volatility_<br>
The current volatility of crypto-currencies makes crypto-denominated loans significantly less attractive for investors and borrowers alike -- if a borrower takes out a loan at a certain interest rate and the value of the currency spikes during the term of the loan, the borrower is on the hook for a much greater interest rate than initially agreed upon, in fiat currency amounts.  In the short term, we mitigate this by giving lenders to option to denominate loan values in fiat currencies instead of crypto currencies (while still executing payments in crypto tokens).  In the long term, we hope easily-available stable tokens and fiat-pegged crypto tokens will fill this gap.  

_Privacy_<br>
An individual’s credit profile and financial history are extremely sensitive data points -- it is crucial for the Dharma Network to guard the privacy of borrowers on the network.  At the same time, lenders need to have access to the requisite data points to make intelligent investment decisions.  This is a conflict inherent to any open lending protocol.  We attempt to bridge the gap by mandating RAAs respect borrower pseudonymity by submitting attestations that do not contain details that could leak aspects of a borrower’s identity -- RAAs merely predict the default risk of whichever pseudonymous entity controls the public key associated with the loan.

<h6 align='center'>ATTACKS</h6>

_Sybil_:<br>
Sybil attacks are detrimental to the Dharma Network insofar as a borrower who is able to take out multiple loans under different assumed identities is not deterred from defaulting by adverse effects on his future credit-worthiness.  The risk-assessment attestation mechanism mitigates this risk to a hopefully workable degree, but there is still a risk of leakage.  For one, identity verification is an imperfect process and a certain portion of borrowers will successfully assume fraudulent identities.  The portion of borrowers who successfully mislead RAAs is a function of the RAA’s competence in identity verification due diligence, however, and RAA accountability metrics should capture the extent to which actual default rates have exceeded the RAA’s predictions.  As such, since the market will gravitate towards investing in loans attested to by the best-rated RAAs, RAAs incentives will be aligned with that of investors in minimizing identity fraud.  Another Sybil vulnerability emerges if a group of RAAs are not cooperating in some capacity, in that a borrower could assume multiple identities by receiving attestations from multiple RAAs.  Tackling this vulnerability requires RAAs cooperate to some degree in order to maintain comprehensive means of cross referencing attestations produced by other RAAs while shielding a borrower’s true identity from the general public -- similarly to how credit bureaus share data with one another in current retail lending schemes.

_Malicious RAAs_:<br>
RAAs must be trusted entities in order for the system to function, since malicious RAAs could fraudulently attest to the credit-worthiness and identity of public keys they control.  To a certain extent, this risk vector should be addressed by the accountability metrics RAAs are evaluated along -- a blatantly fraudulent RAA will have abysmally bad F scores.  The danger emerges in more subtle forms of fraudulent attestation from RAAs who create an illusory sense that their portfolio of attested loans is defaulting at a slightly higher frequency than the predicted or “normal” rate, when, in fact, the counter-party to the lenders’ investments is the RAA itself.  For these reasons, it is crucial that RAAs build trust from investors via a variety of mechanisms, including regulatory oversight and public auditing mechanisms.

_Attestation Replay_:<br>
Once a borrower has received a signed attestation from an RAA, precautions need to be taken to make sure the borrower is unable to broadcast a new loan contract with the same attestation in the future.  As such, as part of the risk-assessment process, the borrower must provide the RAA with the account address the contract will be deployed to.  So long as the contract address is included in the attestation schema signed by the RAA, a replay attack will be detectable by any compliant loan browser.

<h6 align='center'>FUTURE WORK</h6>

At the protocol level, the lion’s share of future work will pertain to releasing standardized schemas for risk-assessment attestations, loan terms, and auditing the security properties of reference loan contract implementations.  There are greenfield opportunities, however, for future research in the following areas:

_Web-of-Trust Risk-Assessment_:<br>
Security and identity models based on webs-of-trust have been extensively researched in the past and even implemented in some cases, most notably in PGP.  Webs-of-trust could prove very powerful in assessing credit-worthiness in a decentralized manner insofar as social capital is an underutilized signal in traditional credit-scoring schemes.  Though the current risk-assessment mechanism relies on centralized RAA entities, future specifications of Dharma could stipulate allowance for decentralized alternatives, such as webs-of-trust.

_Collateralization_:<br>
The emergence and spread of digital assets backed by physical assets (e.g. Digix) allow for conceivable collateralization schemes to be implemented in loan contracts.  Adding secured loans to the Dharma network could encourage investors to enter the network with a higher floor.

_Tranched Loan Assets_:<br>
In traditional lending markets, loan securitization is a major driver of liquidity for a variety of loan types.  Smart contracts can easily replicate the tranched structures of asset-backed securities and serve as a vehicle for engineering a diversified portfolio of loans into more predictable risk-classes.

_Revolving Lines of Credit_:<br>
Americans alone owe an estimated $779 billion in credit card debt -- a testament to the appeal of short-term, revolving lines of credit as a form of debt.  Augmenting the DLS-interface to support revolving lines of credit would allow the Dharma protocol to support what are effectively digital, programmable credit cards.

<h6 align='center'>MISCELLANIA</h6>

_Price-Feed_:<br>
Until stable or fiat-backed tokens are readily usable in the Ethereum ecosystem, loans on the Dharma network will be denominated in fiat currencies and paid out in Ether.  In order to make sure that borrowers and lenders are in concert with respect to the price of ETH at any given time, loan terms must include a specified and reachable price feed with an ETH / fiat trading pair history.  The price feed specification must be committed to on-chain as part of the publicly auditable loan terms. The price feed is primarily used to assess ex post facto whether a repayment to the loan contract was sufficient or if it constituted a partial default.   Since the price feed does not dictate any on-chain logic, it is sufficient for a price feed to be reachable only off-chain.

_Secondary Market Functionality_:<br>
Lenders greatly value a degree of liquidity in their investments, as evinced by the fact that many of the largest peer-to-peer lending platforms have invested heavily in integrating secondary market functionality into their platforms.  Secondary market functionality is baked into the Dharma Loan Standard insofar as loan contracts must implement functionality to transfer a lender’s ownership to a different address.  With several decentralized exchange projects either in production or in the works, we hope to tailor the Dharma Loan Standard’s transfer functionality such that lenders could easily liquidate their loans on these platforms.

<h6 align='center'>FAQ</h6>

1. **Is Dharma.io going to issue a protocol token?**<br>
We currently are not planning on issuing a protocol token or pursuing any sort of token-based crowdfund.

2. **RAAs are centralized entities -- why would the law treat an RAA any differently than peer-to-peer lenders are today?**<br>
In the United States, there is a legal distinction between a bank or lending practice that originates loans and an entity that acts as an Investment Adviser, the latter defined as ‘any person who for compensation engages in the business of advising others as to the value of securities, or as to the advisability of investing in, purchasing or selling securities, or who issues reports or analyses concerning securities as part of a regular business’  Since RAAs do not directly match borrowers to lenders or originate loans in any respect, their function in the legal context much more closely resembles that of an Investment Adviser than that of a lender per se.

3. **What fees does Dharma charge?**<br>
The only fees charged by a third party in the Dharma Loan network are those levied by RAAs -- the Dharma Protocol’s developers and maintainers take no fees!  The rates charged by RAAs are within their discretion, so market conditions will determine the fees associated with different loan sizes and risk-profiles.  We hope low barriers to entry for RAAs will create an environment where fees are competitive to those charged by existing peer-to-peer lending platforms.

4. **Is there a minimum / maximum amount for loans?**<br>
Eventually, we hope to reach a place where the platform supports a healthy ecosystem of loan ticket-sizes and types, ranging from revolving lines of microcredit to mortgages and small-business loans. With that being said, we are initially focusing on short-term micro-loans (e.g. $50 - $500) as we iterate on the fundamental security of the system, and strongly advise against funding or attempting to solicit large-ticket loans in the short-term.

5. **Who will maintain the reference set of DLS-compliant loan contracts?**<br>
Initially, reference loan contracts will be maintained on an open-source repository hosted by Dharma.  Contributors and developers are encouraged to create PRs and bring whatever ideas and innovations they have to the table -- PRs will be merged in at Dharma’s discretion.  As time goes on, we hope to adopt a consortium-based model for maintenance of the DLS-compliant loan contract reference set a la W3C’s recommendations for consistent web standards across browsers.

<h6 align='center'>FOOTNOTES</h6>

<b id="f1"><sup>1</sup></b>BTCJam, BitBond, BitLendingClub [↩](#a1)
