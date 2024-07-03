<ReportNumbers criteria="{criteriaCount}" />

<script>
  import ReportNumbers from './ReportNumbers.svelte';

  import { CriteriaSelected } from '@app/stores/selectedCriteriaStore.js';
  let criteriaCount = 0;
  $: criteriaCount = $CriteriaSelected.length;

  const { translateToObject } = getContext('app');
  const { outcomeValues } = getContext('Evaluation');

  $: TRANSLATED = {
    CRITERIA: $translateToObject('WCAG.SUCCESS_CRITERION')
  };

  $: resultsByCategory = $outcomeValues.reduce(function (final, outcomeValue) {
    let totalEvaluated = 0;
    if ($assertions.length == 0 && outcomeValue.id == 'earl:untested') {
      const value = {
        name: outcomeValue.title,
        id: outcomeValue.id,
        items: $CriteriaSelected
      };
      final.push(value);
    } else if (outcomeValue.id == 'earl:untested') {
      const value = {
        name: outcomeValue.title,
        id: outcomeValue.id,
        items: $assertions.filter(
          (assertion) =>
            assertion.result.outcome.id === outcomeValue.id &&
            assertion.subject.type.indexOf(TestSubjectTypes.WEBSITE) >= 0
        )
      };
      final.push(value);
    } else {
      const value = {
        name: outcomeValue.title,
        id: outcomeValue.id,
        items: $assertions.filter(
          (assertion) =>
            assertion.result.outcome.id === outcomeValue.id &&
            assertion.subject.type.indexOf(TestSubjectTypes.WEBSITE) >= 0
        )
      };
      final.push(value);
    }

    return final;
  }, []);

  function normaliseId(item) {
    return item.test.num.replaceAll('.', '');
  }
</script>
