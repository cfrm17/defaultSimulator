# Credit Default Simulator

The credit default simulator is based on a simplified version credit risk methodology, extended to multiple periods.  Beginning with a portfolio of assets, which are assumed to be in one-to-one correspondence with names, the model simulates a change in the value of the name for each period.  

Given a matrix of ratings transition probabilities, the changes in the valuations of each name are mapped into the matrix, which result in a simulated rating for each name in each period.  Those names that default in period are identified, and the default loss for each period is computed according to the recovery rate assumptions.

The manner in which value changes of the names are simulated is given by the following equation:

  (1)

where   is the simulated value return for the  th asset,   is a standard normal random variable ( ) representing the simulated return of a “market” variable, which is common to all names,   is a standard normal random variable which is specific to each name, and represents the idiosyncratic return of name  , while the correlation  , which is again common to all names, represents the portion of the value return for name   is accounted for by the common market variable.

Equation (1) is the simplest possible factor model.  The GSGDS currently admits only a single uniform correlation for all names in the portfolio to be simulated.  The correlation which is input into the GSGDS is not, however,   from equation (1), but rather is  , since the covariance between the value returns of assets   and   is  , and  .

The extension to multiple periods is accomplished simply by generating a fresh value of   from equation (1) for each name in each period.  Of course, each name is mapped into the transition matrix using the rating with which it begins the period in question.  However, a modification of equation (1) occurs for multiple periods if a feature of the model, known as the economic cycle, is activated. 

Economic cycles are activated by specifying that a given value of   is to be obtained over a user-input number   of consecutive modeling periods.  In the  th period of an economic cycle, the value return is calculated according to

  (2)

where the sub-cycle market return  is given by

 . (3)

from which we can see that  .  Simply put, a particular simulated market return   is considered to occur over   consecutive modeling periods.  At each period, the idiosyncratic portion of the value return is simulated as usual, resulting in a return value for each name according to equation (2).

The mapping of the value returns into the transition matrix is accomplished.  Briefly, it consists of mapping the transition probabilities of the matrix into return value Z-score boundaries.  If the simulated value return of a name crosses one of these Z-scores, a simulated transition event is generated.  Let us consider a transition matrix  , with   different ratings.  Then the probability of a name initially rated   making a transition to rating   is then given as

  (4)

Then the transition boundary Z-scores can be computed according to

 , (5)

which results in a matrix   of transition boundaries.  Given a value   for the return of a name   in a particular period, then we choose the new rating   such that

 . (6)

The implied transitions feature of the model is assumed to be implemented in the following fashion.  Associated with each asset in the portfolio to be modeled there is a credit spread.  This credit spread is converted into a default probability according to the formula

  (7)

where   is the default probability,   is the credit spread associated with the asset at the time horizon  , and   is the assumed recovery rate.  In the case that recovery rates are stochastic, which is permitted in the model, one would use the mean, or expected value of the recovery rate distribution as input to equation (7).  The implied transitions are computed as pairs of ratings taken from the input transition matrix, with a weight which is computed by linearly interpolating the default probabilities obtained from the matrix, that is

  (8)

where   is the default probability of rating   from the  th power of the input transition matrix  .  The power   is obtained to correspond to the time horizon   of the input credit spread.   and   are thus the weights of the ratings   and   which are associated with the asset.  Modeling is then carried out by generating an value return for the name from equation  (1) or (2), and performing the migration according to equation (6) on both of the implied ratings.  If either member of the implied transition pair moves into the default state, then that asset is considered to have defaulted.  At present, the model appears to be restricted to credit spread at the one-year time horizon.  

Default losses are calculated in each period according to the formula

  (9)

where   is the default loss in period  ,   is the face value of asset  , and   is the recovery of  asset  .  The notation   indicates that the sum is to be taken over all assets in the set of defaulted assets in period  .

The model is designed to take into account credit enhancements in several forms.  The first such form of enhancement is a subordinated tranche, which can be set to an arbitrary percentage of the total face value of the asset pool.  The second type of enhancement is the excess spread.  This is effectively a coupon on the remaining total face value of the pool which is paid into an account.  The balance of this account is deducted from the total default losses in each period, and the excess spread account balance is reset.  The third enhancement is the reserve account, which functions identically to the excess spread, except that payments to the reserve account stop when a specified maximum value is reached.

The model does not consider any coupons that may be paid by the assets throughout their life.

Recovery rate

The model enables the user to specify either a constant recovery rate, or a rough “custom” recovery rate distribution.  The specified recovery rate, however, applies identically to each asset in the pool, with no differentiation according to characteristics particular to a given name possible.

Reference:
https://finpricing.com/lib/IrBasisCurve.html

