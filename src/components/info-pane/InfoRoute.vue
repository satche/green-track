<template>
	<div id="route"
		  v-if="data">
		<div class="title">Route</div>
		<ul class="route_infos">
			<li class="route_minutes">
				<strong>Durée:</strong> {{ formattedDuration }}
			</li>
			<li class="route_distance">
				<strong>Distance:</strong> {{ formattedDistance }} km.
			</li>
			<li class="route_ascent">
				<strong>Ascension:</strong> {{ data.features[0].properties.ascent }} m.
			</li>
			<li class="route_descent">
				<strong>Descente:</strong> {{ data.features[0].properties.descent }} m.
			</li>
			<li class="route_steepness">
				<strong>Pente:</strong> {{ this.calculateSteepness.toFixed(2) }}%
			</li>
		</ul>
	</div>
</template>

<script>
import { calculateSteepness } from '../../scripts/utils.js';

export default {
	props: {
		data: Object,
	},

	data() {
		return {
			steepness: 0,
		};
	},

	computed: {
		formattedDuration() {
			if (!this.data) return '';
			let durationInMinutes = this.data.features[0].properties.summary.duration / 60;
			let hours = Math.floor(durationInMinutes / 60);
			let minutes = Math.round(durationInMinutes % 60);
			return hours > 0 ? `${hours} h. ${minutes} min.` : `${minutes} min.`;
		},

		formattedDistance() {
			if (!this.data) return '';
			let distance = this.data.features[0].properties.summary.distance;
			let distanceInKm = distance / 1000;
			let distanceRounded = Math.floor(distanceInKm * 100) / 100;
			return distanceRounded;
		},

		calculateSteepness() {
			if (!this.data) return '';
			let ascent = this.data.features[0].properties.ascent;
			let descent = this.data.features[0].properties.descent;
			let distance = this.data.features[0].properties.summary.distance;
			let steepness = calculateSteepness(ascent, descent, distance, true);
			return steepness;
		},
	},
};
</script>

<style scoped>
#route {
	margin-top: 25px;
}

.route_infos {
	list-style-type: none;
	padding: 0;
}

.route_distance {
	&::before {
		content: "📏";
		margin-right: 0.5rem;
	}
}

.route_minutes {
	&::before {
		content: "⏱";
		margin-right: 0.5rem;
	}
}

.route_ascent {
	&::before {
		content: "⬆️";
		margin-right: 0.5rem;
	}
}

.route_descent {
	&::before {
		content: "⬇️";
		margin-right: 0.5rem;
	}
}

.route_steepness {
	&::before {
		content: "📈";
		margin-right: 0.5rem;
	}
}

@media (max-width: 768px) {
	#route {
		margin-top: 5px;
	}

	.route_infos {
		font-size: 0.8rem;
		margin: 5px 0;
	}
}
</style>