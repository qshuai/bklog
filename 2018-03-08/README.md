### ENV:

- bitcoin-abc version: `855043153da2d7d5d4d577eede5ecf22f2c87080`
- bitcoin-abc modified:

	```
  // src/validation.cpp
  bool IsMonolithEnabled(const Config &config, const CBlockIndex *pindexPrev) {
	   return true;
	     if (pindexPrev == nullptr) {
	         return false;
	     }
	     ...
	```
	
- btcpool version: `04b70fa4ad57e69f1f96ebcc9942b7c76890ae3e`	



