---
id: validators
title: वैलिडेटर्स के लिए सामान्य सवाल
description: "पॉलीगॉन एज के वैलिडेटर्स के लिए सामान्य सवाल"
keywords:
  - docs
  - polygon
  - edge
  - FAQ
  - validators

---

## वैलिडेटर को कैसे जोड़ें/हटाएँ? {#how-to-add-remove-a-validator}

### PoA {#poa}
वैलिडेटर्स को जोड़ना/हटाना वोटिंग द्वारा किया जाता है. आप [यहाँ](/docs/edge/consensus/poa) इस बारे में पूरी गाइड पा सकते हैं.

### PoS {#pos}
आप फ़ंड को कैसे स्टेक करें के बारे में [यहाँ](/docs/edge/consensus/pos-stake-unstake)गाइड पा सकते हैं, ताकि एक नोड, एक वैलिडेटर बन सके और कैसे अनस्टेक करे (वैलिडेटर को हटाएँ).

कृपया नोट करें कि:
- जेनेसिस फ्लैग `--max-validator-count` का इस्तेमाल करके आप अधिकतम संख्या में स्टेकर्स को सेट कर सकते हैं जो वैलिडेटर सेट में शामिल हो सकते हैं.
- जेनेसिस फ्लैग `--min-validator-count ` का इस्तेमाल करके आप न्यूनतम संख्या में स्टेकर्स को सेट कर सकते हैं जिन्हें वैलिडेटर सेट (`1` में बाय डिफ़ॉल्ट) शामिल करने की आवश्यकता है.
- अधिकतम वैलिडेटर की संख्या पूरी होने पर, जब तक आप सेट में पहले से मौजूद एक वैलिडेटर को हटा नहीं देते तब तक दूसरा इसमें नहीं जोड़ सकते (इससे कोई फ़र्क नहीं पड़ता अगर नए वाले की स्टेक की गई रकम अधिक है). अगर आप एक वैलिडेटर को हटाते हैं, तो बाकी बचे हुए वैलिडेटर्स की संख्या `--min-validator-count` से कम नहीं हो सकती.
- `1` की एक डिफ़ॉल्ट सीमा है,  एक वैलिडेटर बनने के लिए स्थानीय नेटवर्क (गैस) की एक करेंसी है.



## एक वैलिडेटर के लिए डिस्क में कितने स्पेस का सुझाव दिया जाता है? {#how-much-disk-space-is-recommended-for-a-validator}

हम एक रूढ़िवादी अनुमानित रनवे के रूप में 100G के साथ शुरू करने की सलाह देते हैं, और सुनिश्चित करें कि बाद में डिस्क को मापना संभव हो.


## क्या वैलिडेटर्स की संख्या के लिए कोई सीमा है? {#is-there-a-limit-to-the-number-of-validators}

अगर हम तकनीकी सीमाओं की बात कर रहे हैं, तो पॉलीगॉन एज में स्पष्ट रूप से नेटवर्क में आपके नोड्स की संख्या के लिए कैप नहीं है. आप कनेक्शन कैप को हर नोड को (इनबाउंड / आउटबाउंड कनेक्शन काउंट्स) के आधार पर सेट कर सकते हैं.

अगर हम व्यावहारिक सीमाओं के बारे में बात करें तो आप 100 नोड के क्लस्टर का प्रदर्शन 10 नोड के क्लस्टर से खराब देखेंगे. अपने क्लस्टर में नोड्स की संख्या को बढ़ाकर आप संचार की जटिलता और सामान्य रूप से नेटवर्किंग ओवरहेड को बढ़ा देते हैं. यह सब इस बात पर निर्भर करता है कि आप किस तरह के नेटवर्क को रन कर रहे हैं और आपके पास किस तरह के नेटवर्क की टोपोलॉजी है.

## PoA से PoS में कैसे स्विच करें? {#how-to-switch-from-poa-to-pos}

PoA सहमति का डिफ़ॉल्ट मैकेनिज्म है. एक नए क्लस्टर के लिए, PoS में स्विच करने के लिए, आपको जेनेसिस फ़ाइल जनरेट करते समय `--pos` फ्लैग को जोड़ने की ज़रूरत होगी. अगर आपके पास एक रन कर रहा क्लस्टर है, तो आप [यहाँ](/docs/edge/consensus/migration-to-pos) देख सकते हैं कि इसे कैसे स्विच किया जाए. हमारे सहमति के मैकेनिज्म और सेटअप के बारे में आपको सभी जानकारी हमारे [सहमति सेक्शन](/docs/edge/consensus/poa) में मिल सकती है.

## ब्रेकिंग बदलाव होने पर मैं अपने नोड्स को कैसे अपडेट करूँ? {#how-do-i-update-my-nodes-when-there-s-a-breaking-change}

इस प्रक्रिया को कैसे करें, जानने के लिए आपको [यहाँ](/docs/edge/validator-hosting#update) एक गाइड मिल सकती है.

## क्या PoS एज के लिए स्टेकिंग की न्यूनतम रकम कॉन्फ़िगर करने योग्य है? {#is-the-minimum-staking-amount-configurable-for-pos-edge}

स्टेकिंग के लिए बाय डिफ़ॉल्ट न्यूनतम रकम `1 ETH` है, और यह कॉन्फ़िगर करने योग्य नहीं है.

## क्यों JSON RPC की `eth_getBlockByNumber` और `eth_getBlockByHash` कमांड्स माइनर के पते को रिटर्न नहीं करतीं? {#not-return-the-miner-s-address}

पॉलीगॉन एज में वर्तमान में इस्तेमाल की जाने वाली सहमति IBFT 2.0 है, जो बदले में, Clique PoA में वोटिंग के मैकेनिज्म के बारे बताए गए अनुसार बनता है: [ethereum/EIPs#225](https://github.com/ethereum/EIPs/issues/225).

EIP-225 (Clique PoA) को देखते हुए, यह एक प्रासंगिक हिस्सा है जो बताता है कि क्या `miner`(उर्फ `beneficiary`) को इस्तेमाल करते हैं:

<blockquote>हम ethash हेडर फील्ड को इस तरह पुनर्निर्धारित करते हैं:
<ul>
<li><b>लाभार्थी / माइनर: </b> अधिकृत साइनर्स की सूची को संशोधित करने का प्रस्ताव करने के लिए पता</li>
<ul>
<li>सामान्य रूप से ज़ीरो से भरे जाने चाहिए, केवल वोटिंग के समय संशोधित किया जाना चाहिए.</li>
<li>वोटिंग मैकेनिक्स के आसपास कार्यान्वयन में अतिरिक्त जटिलता से बचने के लिए मनमानी वैल्यूज़ की अनुमति है (यहाँ तक कि अर्थहीन जैसे कि नॉन-साइनर्स को वोट देना).</li>
<li> चेकपॉइंट (जैसे epoch से गुज़रना) के ब्लॉक्स पर अवश्य ही ज़ीरोज़ से भरा जाना चाहिए. </li>
</ul>

</ul>

</blockquote>

इस प्रकार, `miner` फ़ील्ड का इस्तेमाल एक खास पते को वोट करने के प्रस्ताव के लिए इस्तेमाल किया जाता है, न कि ब्लॉक को प्रस्तावित करने वाले को दिखाने के लिए.

ब्लॉक को प्रस्तावित करने वाले की जानकारी ब्लॉक हेडर में RLP के एन्कोड किए गए इस्तांबुल के अतिरिक्त डेटा फ़ील्ड के सील फ़ील्ड से पबकी को रिकवर करके प्राप्त की जा सकती है.

## जेनेसिस के कौन-से हिस्सों और वैल्यू को सुरक्षित रूप से संशोधित किया जा सकता है. {#which-parts-and-values-of-genesis-can-safely-be-modified}

:::caution

कृपया इसे संपादित करने के प्रयास से पहले मौजूदा genesis.json की मैनुअल कॉपी बनाना सुनिश्चित करें. इसके अलावा, genesis.json फ़ाइल को संपादित करने से पहले पूरी चेन को रोकना पड़ता है. जेनेसिस फ़ाइल को संशोधित करने के बाद, इसके नए संस्करण को सभी non-validator और वैलिडेटर नोड्स में वितरित करना पड़ता है.

:::

**जेनेसिस फ़ाइल के बूटनोड्स सेक्शन को ही सुरक्षित रूप से संशोधित किया जा सकता है**, जहां आप सूची से बूटनोड्स को जोड़ या हटा सकते हैं.