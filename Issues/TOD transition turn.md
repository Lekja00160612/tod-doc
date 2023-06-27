---
tags: [" #issue/available_solution ", " #issue/policy_manager"]
---
- Occur between multiple different  [[TOD task]]s or [[TOD domain]]s, highly depends on the settings during setup stage.

## SGD transition turn

### Status
- In SGD, all transition turns happen between 2 [[TOD domain]]s, represent by multiple `frames`.
- We can ensure (by executing the code below) that every transition turn has one `frame` exist `inform_intent` action, this help us easier to continue process the dataset, and, this behavior also matched with our understand of TOD granularity levels.
```(python)
if len(turn["frames"]) > 1:

        acts = []

        for frame in turn['frames']:

            [acts.append(action['act'].lower()) for action in frame['actions']]

        if "inform_intent" not in acts:

            logging.warning(f"{turn_info.dialogue_id} {turn_id}")

            logging.warning(f"{state_dict['conversation'][-2:]}")

            for frame in turn['frames']:        

                logging.warning(f"<{frame['service']}>: {[action['act'] for action in frame['actions']]}")

    return {}, []
```
### Issues
- How should we process the dataset?
	- During the first domain, every actions in [[history]] is available. However, to process other domains require much more effort as from the second domain appearance onwards, all actions of previous domains in the conversations are no longer available in the input prompt, in this case, we must replace them with some place holder, for example [[OOD]] actions. ![[OOD#Actions]]
	- The problem is multiple `inform` and `request` actions might appear in one history turn, should we
		- 1. replace ALL `inform` and `offer` actions with ONLY ONE [[inform non-defined]], ALL `request` actions with ONLY ONE [[request non-defined]]
		- 2. replace EACH `inform` and `offer` actions with ONE [[inform non-defined]], EACH `request` actions with ONE [[request non-defined]]
	- Other actions include `request_alts`, `goodbye`, `affirm`, `negate`, `select` always appear in any domain, keeping them is common across dataset examples.
	- Even though these solutions are temporary, they work perfectly for now and should be improved in the future.